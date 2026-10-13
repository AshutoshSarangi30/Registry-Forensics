## Registry Forensics - Day 02
###### By - Ashutosh Sarangi

1\. **Aim**: To study the NTUSER.dat hive and get an idea on the user's behaviour and activity.
   
2\. **Tools Used**: Registry Explorer, Registry Editor.

3\. **Theory**: NTUSER.dat is your personality on your system. Think of it as a complete database of your system. Your preferences as a user, the applications and programs you use and even the external drives you connect to your system. We can also make an estimated timeline by analysing the timestamps of the files accessed and the activities in their File Explorer. Today we will dive into a system’s NTUSER.dat and see what we can gather about the user.

**How is it different from USRCLASS.dat?**
USRCLASS.dat only keeps a track of the folders accessed using the user interface and other application settings. However, NTUSER.dat is a comprehensive record of each and every minute activity which a user performs on his/her system. Right from inserting an external drive to executing a file, everything is recorded in NTUSER.dat.

4\. **Activity**: a. There is no specific way we can go to NTUSER.dat. Actually NTUSER.dat exists for a certain Windows user profile. Now we cannot open this on the Registry Editor since Windows locks it when someone has logged in to the user profile. We can either access it using the Registry Explorer (easy to understand and use) or use a triage tool like FTK Imager (more complex).

b. Now our work is to perform a series of tasks to analyse the user behaviour.
  **I. Task 1 - Access the recently accessed files** - The first task is to see which files have been recently accessed by the user. From a forensic investigator’s perspective, it is one way to know which files have been recently accessed on the system. So now we visit the key ‘RecentDocs’ to see those particular files:

```dos
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs
```

<img src="https://github.com/user-attachments/assets/c2e8ae9d-b5ab-400b-bc99-0ad5db00c917" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<div align="center">
These are the files which are recently accessed by the user. Note how they have been sorted on the basis of their file extensions.
</div>

But how do we know which files have they accessed EXACTLY? Whatever we are seeing is just records of some ‘REG_BINARY’ type files with some hexadecimal gibberish in the name of data.
Here comes **Registry Explorer**. It is a tool used to decode this and tells us which files were accessed by the user. We can also access the previously locked NTUSER.dat hive using this tool's 'Live System' feature. On the flip side, we cannot see the actual data of those files. However, remember that in digital forensics our aim is to just prove that a certain file, which did not belong to the accused’s system, existed in it.

<img src="https://github.com/user-attachments/assets/0a24f472-f801-469c-addd-84cd1c1f9aa9" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />

Bingo! We can see the file names of the files accessed by the user. Again as an investigator, this is our aim. Proving that a certain file existed in a place where it does not belong. This marks the end of Task I.

  **II. Task 2 - Recording the programs** - We can access these by going to the following location:

```dos
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist
```

<img src="https://github.com/user-attachments/assets/102ebd6f-dd9d-4af8-a00b-de641c263cd7" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<div align="center">
These are the folders where all our recently run programs are stored. Here, note that all the folders except CEBFF… and F4E57… have programs which are not recognisable to us. Actually all these folders are encrypted using the ROT13 cipher. It is Registry Explorer which decrypts them. Yet we are able to see only the two aforementioned folders since they are the only ones tracking applications we use everyday.
</div>
<img src="https://github.com/user-attachments/assets/db8afe2a-ccdf-4acf-b3f2-d91aaf53fe8f" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<br>
<img src="https://github.com/user-attachments/assets/1a015547-dc1c-42fa-aac2-300c86d92d11" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<div align="center">
These are the recently accessed programs by the user.
</div>

  **III. Task 3 - Address bar history and search bar history** - This might sound uncanny but whatever you type in your File Explorer’s address bar and search bar is also stored by your system. We can get it by going to the following location:
```dos
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\TypedPaths
```

<img src="https://github.com/user-attachments/assets/bd5682ff-bd5b-4e00-9da5-394258def702" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<div align="center">
We have obtained a list of 21 most recently typed URLs in the File Explorer address bar.
</div>

Now to visit the search history of the search bar go to the following location:
```dos
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\WordWheelQuery
```

<img src="https://github.com/user-attachments/assets/72b74f6f-8e49-41a8-b30f-3225d98bcfe4" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<div align="center">
Results of the Windows Search Box history 
</div>

We see two words - ‘sudi’ and ‘samit’. However, these are not foolproof. A limitation of Registry Explorer is that it cannot exactly predict the word length of the word which was typed in the search bar. Add to that the fact that Windows allots a fixed size for these search keywords and if the words are not as long as the fixed size then Windows fills the rest of the places with older, garbage values.
Another possibility is that since the Registry Explorer doesn’t know the exact length of the word, even after getting the full word it pulls the other hex bytes next to it creating a meaningless word. So, for instance, if the user has typed ‘sud’, the Registry Explorer, not knowing the exact length, has added an extra ‘i’ to the term.
Adding insult to injury, there is no way in modern Windows we can recover what exactly the user was trying to search. This is because all the history of the search box is stored in a Windows Search Index Framework (or maybe even a local SQLite database). And things get even more hopeless if the system is not a company machine or a heavily optimised gaming
Machine (these machines have a feature called Group Policy where we can enable recording of the search history of the search box).

<img src="https://github.com/user-attachments/assets/f88f7442-80ff-463f-bf54-5e6a2eeff6b4" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<div align="center">
This implies that the system we are working on is using a Windows Home Edition.
</div>

**IV. Task 4 - Looking for evidence in Win+R dialogue box** - This is another unusual place to look for evidence but as an investigator we must look at all the possible locations in an accused’s system. Visit the following location:
```dos
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\RunMRU
```

<img src="https://github.com/user-attachments/assets/d0da9285-76bd-4fe1-bef1-99707249b1e2" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<div align="center">
These are the recent commands run by the user in Win+R dialogue box.
</div>

**V. Task 5 - Checking if the user connected any external device to the system** - Another important task. During an investigation, we must know if the accused had inserted any external device into the system to exchange files. Go to the following location:

```dos
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\MountPoints2
```

<img src="https://github.com/user-attachments/assets/be49bdf7-c7ce-442b-b67d-8f30f36f75ad" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<div align="center">
Look at the red box. All these are evidence that an external storage device was connected to this system for this user account.  
</div>

Right so we have a list of all the external storage devices which were connected to this system for the user account whose NTUSER.dat we are scanning. However, how do we know if it was a USB or an external hard drive? Well the issue is MountPoints2 only tells us that ‘Yes there was an external storage drive here’, but it cannot tell us what exactly was that. For that we will need to go to the SYSTEM hive.

5\. **Conclusion**: NTUSER.dat acts as a database of all the system’s activities. Analysing this will help us get a good idea of what has happened in the system. In fact one should start with this hive first and then dive into USRCLASS.dat to get more information.

6\. **Easter Eggs**: a. Note the green box in Task I. It is a list of all the articles opened by the user on Microsoft Edge. This is a wild card while trying to understand the personality of the user.

b. 
```dos
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\RunOnce
HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run
HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\RunOnce
```
During malware analysis, these keys are checked to look for the malware. This is so because these are some keys where “persistence” exists i.e. any program here keeps running.

7\. **Next Session**: Understanding SYSTEM hive.
