## Registry Forensics - Day 01 (part-02)
###### By - Ashutosh Sarangi

1. **Aim**: To locate and retrieve a deleted file using LNK Analysis, FTK Imager and Autopsy.

2. **Tools Used**: LECmd.exe, FTK Imager, Autopsy, Microsoft Excel, Microsoft Word.

3. **Theory**: In the previous session we had recovered a file which was no longer in a particular folder. Today we will recover a file which is apparently "permanently deleted" from our system.

First let us understand LNK properly. From the last session we know that LNK files left a "breadcrumb" as soon as a file entered our system. Well that's not exactly the case. Suppose you download and open a text file which your friend sent you. Now as soon as you open that text file, a "breadcrumb" i.e. an LNK file recording that activity is created automatically and at the same time the same log is recorded in Notepad's (or any other text editor which you may use) jump list.

Now this may raise the question that if both of these work in tandem then how is LNK the "backup" of jump lists? Well there are a number of scenarios where this is true. They are as follows:

a. Every application on your system has its own Jump List and these Jump Lists have a limit. As in they can only show you the most recent "n" files. Here, using LNK files we can get all those "n+" files as well.

b. Folders do not open in any application. There is no Jump List for them. To see which folder(s) the user has accessed we need LNK files.

c. In many cases, an unauthorised user may access files which they are not supposed to and then clear their recent file history or even delete the Jump Lists. However, since they had opened the file, LNK stores a record of all that.

d. This one is a bit unusual but imagine you do use some third-party file manager instead of Windows Explorer which bypasses the Windows API monitoring the Jump Lists. In this case as well LNK files will be handy.

Another terminology which will be useful is **MFT** or **Master File Table**. It is a database of all the files and folders in our system. Each file or folder has a file index and a flag which will be useful to mark if a file is active or deleted.

Also, before proceeding with the activity, we must understand how deletion works on our system. So when we permanently delete a file, Windows gets a command that "Alright you can use the space allotted to file x for other purposes since the user doesn't need it anymore". But actually the file is always sitting quietly on your hard drive. A freshly deleted file's flag changes to 'deleted' which implies that its MFT index entry is open to be overwritten.


4. **Activity**: a. We will try to retrieve a file named "Types of writing.docx" (which is permanently deleted from my system). So first let us check our Jump List.

<img width="852" height="452" alt="image" src="https://github.com/user-attachments/assets/6de898bb-2471-4636-a2f1-a3ae63aca618" />
<br>
<div align="center">
Found the record for the target file in the Jump List 
</div>
<br>
b. The Jump Lists gives us a location. Now let us check the LNK files using LECmd.exe to see if we can get anything there. Run the following command to perform LNK analysis:      <br>
<br>

     LECmd.exe -d "C:\Users\<YourUsername>\AppData\Roaming\Microsoft\Windows\Recent" --csv "C:\Users\<YourUsername>\Desktop" --csvf "LNK_Analysis.csv"

<br>

Now we'll look for our target file. It is not amongst the LNK files.

<img width="1912" height="1020" alt="image" src="https://github.com/user-attachments/assets/80b4cbfb-f24e-49dd-9aab-7a2160fd0044" />
<br>
<div align="center">
No records for the target file after LNK analysis
</div>
<br>

c. Now visiting this location will not help our case as the file is already deleted. Enter **FTK Imager**. We first click on 'File' and go to 'Add Evidence Item'. After that you will be asked to choose which drive you want to scan. In our case we will go with C drive.

<img width="1917" height="1017" alt="image" src="https://github.com/user-attachments/assets/3ffa30a7-fa58-4054-bf4a-358b0785b80e" />
<br>
<img width="1917" height="1020" alt="image" src="https://github.com/user-attachments/assets/375d8a9d-b871-4b30-9b25-d0257a256b0f" />
<br>
<img width="1917" height="1016" alt="image" src="https://github.com/user-attachments/assets/27e43a06-e892-4bdd-a541-803b854e3128" />
<br>
<div align="center">
Working on FTK Imager
</div>
<br>

d. Our next step is to locate the target file. So we navigate through the evidence tree and find the file manually (tedious but no other option unfortunately). On finding the file right-click on it and export it to your desktop.

<img width="1917" height="1017" alt="image" src="https://github.com/user-attachments/assets/8ab46290-2704-4407-abd5-d9f410a2db3a" />
<br>
<div align="center">
Found the target file after FTK Imager scans the system’s disks
</div>
<br>

e. Now when we try to open we get a blank document. Note how the document has 12 pages. This is the number of pages the deleted document had. This is a sign that we may still be able to recover the data. Here Autopsy steps in.

<img width="1917" height="1020" alt="image" src="https://github.com/user-attachments/assets/4fa89198-9c74-460c-80ae-e1443a25dccf" />
<br>
<div align="center">
The blank document after we extracted it from FTK Imager
</div>
<br>

f. Open Autopsy and click on 'New Case'. Then go to 'Add Data Source' and enter the case name, set up the data source and click 'Finish'. The tool starts scanning the drive which you selected.

<img width="1702" height="905" alt="image" src="https://github.com/user-attachments/assets/3bb1df8c-f9e7-490b-a816-3dd6ad1424a4" />
<br>
<img width="1917" height="1017" alt="image" src="https://github.com/user-attachments/assets/5d83a575-b9f8-4e8a-9db9-b742dfa0c496" />
<br>
<img width="1917" height="1017" alt="image" src="https://github.com/user-attachments/assets/75a8e6f8-eb01-454b-8b86-88c560f00c6f" />
<br>
<div align="center">
Setting up Autopsy
</div>
<br>

g. Go to ‘Deleted Files’, then ‘All’ and manually find the target file.

<img width="1917" height="1017" alt="image" src="https://github.com/user-attachments/assets/b2805941-f7d4-4d9f-9ac5-6608a9cacb99"/>
<br>
<div align="center">
Found the target file in Autopsy
</div>
<br>

h. After opening the recovered file we see this error. Now go to ‘File’ and open the target file as follows:
<br>
<br>
<img width="1917" height="1020" alt="image" src="https://github.com/user-attachments/assets/bc804fd3-642d-4eea-b125-6f1130fcc54c" />
<br>
<br>
<img width="972" height="517" alt="image" src="https://github.com/user-attachments/assets/89cf7149-7c2d-4721-890f-5bb74a349c39" />
<br>
<div align="center">
Attempting to recover the data of the target file
</div>
<br>

i. This is a dead end. Our investigation ends here and the data could not be recovered.

<img width="1917" height="1020" alt="image" src="https://github.com/user-attachments/assets/9cb2de60-1d2c-4d9a-9c66-f4a3f6d2f238" />
<br>
<div align="center">
The “data” we recovered is nothing but gibberish which means the original data has been overwritten and cannot be retrieved
</div>
<br>

5. **Conclusions**: a. We couldn’t find the data because the data clusters which Windows had allotted it have been overwritten. Our file’s data is permanently gone. However, if we can prove that a certain file actually existed in a person’s computer and that he or she had accessed it (using Jump Lists), that is a solid proof.

b. The presence of the file’s record in the Jump List implies that the user opened the file and it was recorded by Microsoft Word’s Jump List. However, its absence in the LNK file records implies that the user moved the file to another folder from the one where it was downloaded and then opened it.

c. Always open Autopsy as administrator.

d. This was the end of USRCLASS.dat hive. We learnt how to find the current location of a file which is “lost” and we also saw how we can retrieve a deleted file and how its mere presence can affect investigations and court cases in favour of the prosecution.

6. **Next Session**: Navigating and understanding the next hive - NTUSER.dat.
