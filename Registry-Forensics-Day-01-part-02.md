## Registry Forensics - Day 01 (part-02)
###### By - Ashutosh Sarangi

1\. **Aim**: To locate and retrieve a deleted file using LNK Analysis, FTK Imager and Autopsy.

2\. **Tools Used**: LECmd.exe, FTK Imager, Autopsy, Microsoft Excel, Microsoft Word.

3\. **Theory**: In the previous session we had recovered a file which was no longer in a particular folder. Today we will recover a file which is apparently "permanently deleted" from our system.

First let us understand LNK properly. From the last session we know that LNK files left a "breadcrumb" as soon as a file entered our system. Well that's not exactly the case. Suppose you download and open a text file which your friend sent you. Now as soon as you open that text file, a "breadcrumb" i.e. an LNK file recording that activity is created automatically and at the same time the same log is recorded in Notepad's (or any other text editor which you may use) jump list.

Now this may raise the question that if both of these work in tandem then how is LNK the "backup" of jump lists? Well there are a number of scenarios where this is true. They are as follows:

a. Every application on your system has its own Jump List and these Jump Lists have a limit. As in they can only show you the most recent "n" files. Here, using LNK files we can get all those "n+" files as well.

b. Folders do not open in any application. There is no Jump List for them. To see which folder(s) the user has accessed we need LNK files.

c. In many cases, an unauthorised user may access files which they are not supposed to and then clear their recent file history or even delete the Jump Lists. However, since they had opened the file, LNK stores a record of all that.

d. This one is a bit unusual but imagine you do use some third-party file manager instead of Windows Explorer which bypasses the Windows API monitoring the Jump Lists. In this case as well LNK files will be handy.

Another terminology which will be useful is **MFT** or **Master File Table**. It is a database of all the files and folders in our system. Each file or folder has a file index and a flag which will be useful to mark if a file is active or deleted.

Also, before proceeding with the activity, we must understand how deletion works on our system. So when we permanently delete a file, Windows gets a command that "Alright you can use the space allotted to file x for other purposes since the user doesn't need it anymore". But actually the file is always sitting quietly on your hard drive. A freshly deleted file's flag changes to 'deleted' which implies that its MFT index entry is open to be overwritten.


4\. **Activity**: a. We will try to retrieve a file named "Types of writing.docx" (which is permanently deleted from my system). So first let us check our Jump List.

<img src="https://github.com/user-attachments/assets/eefbd773-7d7c-4553-95cc-c55115fdc69e" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<div align="center">
Found the record for the target file in the Jump List 
</div>

b. The Jump Lists gives us a location. Now let us check the LNK files using LECmd.exe to see if we can get anything there. Run the following command to perform LNK analysis:      

     LECmd.exe -d "C:\Users\<YourUsername>\AppData\Roaming\Microsoft\Windows\Recent" --csv "C:\Users\<YourUsername>\Desktop" --csvf "LNK_Analysis.csv"

Now we'll look for our target file. It is not amongst the LNK files.

<img src="https://github.com/user-attachments/assets/b7d0ebaf-c982-4c27-aa90-8697d11196b3" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<div align="center">
No records for the target file after LNK analysis
</div>

c. Now visiting this location will not help our case as the file is already deleted. Enter **FTK Imager**. We first click on 'File' and go to 'Add Evidence Item'. After that you will be asked to choose which drive you want to scan. In our case we will go with C drive.

<img src="https://github.com/user-attachments/assets/3a88607f-9aac-40bb-a8e3-376a9ce22f7a" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<br>
<img src="https://github.com/user-attachments/assets/eba5e23b-d1b1-4d4f-acb6-44a7183663b5" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<br>
<img src="https://github.com/user-attachments/assets/8b7944ca-bbc8-4c11-96e9-4dcbdc6106d7" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<div align="center">
Working on FTK Imager
</div>

d. Our next step is to locate the target file. So we navigate through the evidence tree and find the file manually (tedious but no other option unfortunately). On finding the file right-click on it and export it to your desktop.

<img src="https://github.com/user-attachments/assets/dcd27fe7-60d2-4ebf-8114-90c0f2082a7f" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<div align="center">
Found the target file after FTK Imager scans the system’s disks
</div>

e. Now when we try to open we get a blank document. Note how the document has 12 pages. This is the number of pages the deleted document had. This is a sign that we may still be able to recover the data. Here Autopsy steps in.

<img src="https://github.com/user-attachments/assets/426c7206-ae85-4279-9a65-1d8b2cb18a3c" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<div align="center">
The blank document after we extracted it from FTK Imager
</div>

f. Open Autopsy and click on 'New Case'. Then go to 'Add Data Source' and enter the case name, set up the data source and click 'Finish'. The tool starts scanning the drive which you selected.

<img src="https://github.com/user-attachments/assets/29d732d1-99fd-4761-9c3b-579a761a4b0d" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<br>
<img src="https://github.com/user-attachments/assets/2d305f3a-2806-4d7a-b207-43b231ddae45" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<br>
<img src="https://github.com/user-attachments/assets/a5fd199b-900f-4ce1-b995-594ef6eebafa" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<div align="center">
Setting up Autopsy
</div>

g. Go to ‘Deleted Files’, then ‘All’ and manually find the target file.

<img src="https://github.com/user-attachments/assets/8bd4d848-17d2-4246-8ec1-590271d6af12" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<div align="center">
Found the target file in Autopsy
</div>

h. After opening the recovered file we see this error. Now go to ‘File’ and open the target file as follows:
<img src="https://github.com/user-attachments/assets/c55fb1b2-99de-4e73-91ec-b33ce168a893" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<br>
<img src="https://github.com/user-attachments/assets/15e7ab72-0f3a-48f1-87a2-5bdc911cd143" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<div align="center">
Attempting to recover the data of the target file
</div> 

i. This is a dead end. Our investigation ends here and the data could not be recovered.

<img src="https://github.com/user-attachments/assets/6091d1e5-7fc6-4408-b56b-6087476ee9cc" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<div align="center">
The “data” we recovered is nothing but gibberish which means the original data has been overwritten and cannot be retrieved
</div>

5\. **Conclusions**: a. We couldn’t find the data because the data clusters which Windows had allotted it have been overwritten. Our file’s data is permanently gone. However, if we can prove that a certain file actually existed in a person’s computer and that he or she had accessed it (using Jump Lists), that is a solid proof.

b. The presence of the file’s record in the Jump List implies that the user opened the file and it was recorded by Microsoft Word’s Jump List. However, its absence in the LNK file records implies that the user moved the file to another folder from the one where it was downloaded and then opened it.

c. Always open Autopsy as administrator.

d. This was the end of USRCLASS.dat hive. We learnt how to find the current location of a file which is “lost” and we also saw how we can retrieve a deleted file and how its mere presence can affect investigations and court cases in favour of the prosecution.

6\. **Next Session**: Navigating and understanding the next hive - NTUSER.dat.
