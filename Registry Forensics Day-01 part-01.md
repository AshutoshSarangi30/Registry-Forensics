## Registry Forensics - Day 01 (part-01)

- 1. Aim: To understand the different hives in the Registry and find a file whose location has changed using the USRClass.dat hive ShellBags and Jump Lists.

- 2. Tools Used: ShellBags Explorer, JLECmd.exe, Registry Editor, Microsoft Excel.

- 3. Theory: Registry contains all the information that Windows needs to function on a day-to-day basis. It has separate files containing the necessary information. The hives are as follows:

- a. SYSTEM (c:\Windows\System32\config\SYSTEM)

- b. SECURITY (c:\Windows\System32\config\SECURITY)

- c. SAM (c:\Windows\System32\config\SAM)

- d. SOFTWARE (c:\Windows\System32\config\SOFTWARE)

- e. NTUSER.DAT (c:\Users\username\NTUSER.DAT)

- f. USRCLASS.DAT

(C:\Users\username\AppData\Local\Microsoft\Windows\USRCLASS.DAT)

In today's session we have focused on USRCLASS.DAT. USRCLASS.DAT (along with NTUSER.DAT - more on that later) is basically the whole database of your activity on your laptop/computer. USRCLASS.DAT focuses on your app data, folder layouts and background settings (for instance when you open a .mp3 file you set the 'Open With' preference as 'Windows Media Player'). It stores all the files and folders which you may have executed or accessed or viewed in recent history. However, it is not possible to store all the data right from the moment you start using it. Therefore Windows has a feature where you can view only the most recent "n" items. Another catch is that when you format your system, everything in your system is overwritten (in simple terms your OS forgets everything).

We have used two primary features of Windows OS today - ShellBags and Jump Lists.

- a. ShellBags - Think of it as the "visual search history" of your system. For instance if you go to your Downloads folder and set 'View Icons' as 'Large', then that is saved in the ShellBag. It exists inside the USRCLASS.dat.

- b. Jump Lists - These are sitting in your hard drive. They have information on EVERY FILE which you view, access or execute on your system. However, there is a catch here. If you do not do any of the aforementioned activities then Jump List doesn't store that. Every application has its own Jump List provided it has the provision for storing history.

- c. LNK Files - This covers up the loophole left by Jump Lists. Every time a file lands in your system, a "breadcrumb" is left and stored as an LNK file. This is crucial in situations where you have to recover data which is permanently deleted.


- 4. Activity: Now our target today is to locate a file which was stored in C drive long ago but its current location is not known.

- a. Open Registry Editor and go to USRCLASS.dat hive using:

*We are now in USRCLASS dat hive*

- b. Now visit the ShellBags using:

*These are the ShellBags along with their (gibberish) hexadecimal data*

- c. We see a lot of files of ‘REG_BINARY' type with hexadecimal-coded data. Here our first tool comes into use i.e. ShellBags Explorer which decodes this hexadecimal data into a format which we can understand.


- d. Run a 'Load Active Registry' in ShellBags Explorer (note - run ShellBags Explorer as administrator). This will decode the ShellBags.

*Successfully decoded the “gibberish”*

- e. This is the same hexadecimal data which seemed gibberish to us moments ago. Now we see a structure familiar to our regular File Explorer. Here let us assume we need to find the current location of the marked file below. How do we do that?

*Let this be our target folder for this session.*

- f. Enter JLECmd.exe. As discussed before Jump Lists will have it noted down if you view, access or execute a file. We use this to find the current location of our chosen file.


- g. Run the following command in cmd (note - open the cmd in the folder where you have the JLECmd.exe) and it will export the full record of the Jump Lists recorded in your system. Note that your decoded ShellBags show the folders you explored and Jump Lists go inside those folders and tell you which files were accessed.

## JLECmd.exe -d

“C:\Users\<YourUsername>\AppData\Roaming\Microsoft\Windows\Recent\AutomaticD estinations” --csv “C:\Users\<YourUsername>\Desktop”

*The CSV file of your system’s Jump Lists*

- h. Use the Filter feature and enter the keywords of the target file and we get the current location of that file.

This is the output of the filter. We see multiple files having the same name as the filter we have applied. So now we can individually visit these files to get the desired one. For now let’s say we want

to access the first file.


- i. Now we copy the file path and paste it in File Explorer and hey! The target file opens!

This is the file we were looking for. On the right we entered the file location in the search bar of the

File Explorer and it opened the target file (on the left) for us.

- 5. Conclusion: In this session we found out how we go about finding a file which is "lost" somewhere in our system. But what if the file is deleted?

- 6. Easter Eggs: i. You can get your Security ID (SID) by running the following command on cmd:

ii. Look at the circle in 3(e). We see a tick mark in the box. This is the ‘Has Explored’ section and it implies that the user visited that file/folder and EXPLICITLY made changes to it. Very crucial information as far as forensics is concerned.

- 7. Next Session: Using LNK to retrieve deleted data.
