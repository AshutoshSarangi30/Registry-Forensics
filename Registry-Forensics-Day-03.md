## Registry Forensics - Day 03
###### By - Ashutosh Sarangi

1. **Aim** : To understand the data stored in SYSTEM hive.

2. **Tools Used** : Registry Editor

3. **Theory** : So far we have not really gotten into any dedicated hive. We dealt with USRCLASS.dat and NTUSER.dat which were basically huge databases storing the user personality over the system and all the user-specific information and application history over the system. Today we go into SYSTEM hive. What does it do you ask? SYSTEM hive stores the system-level and OS-level information. It also stores basic information on the bootloader. Besides that there are records of the HID and USB devices connected to the system, the keyboard layouts based on regions and so on. Today we will understand the work of certain specific folders of this hive.
When you explore this hive you will see there are numerous folders here and you may wonder that the writer is too lazy to cover everything. But trust me getting into each and every folder and finding out and interpreting what information it stores is one crazy job. Either way, we are always open to learning new stuff so maybe some other day!

4. **Activity** - In this session we will look at some important folders in the SYSTEM hive and what their work is. So first let us get into the hive by visiting the following:

  ```dos
HKEY_LOCAL_MACHINE\SYSTEMF
   ```
i. **CurrentControlSet** points to the active system configuration and the current session. Sounds complicated? Well a control set stores all the hardware drivers, services and other requirements to run the OS. Windows keeps two control sets - one as the main session and the other as a backup in case there is a mishap while opening the primary session.

One of the first things we see here is **hivelist**. As the name suggests, it is a list of ALL the hives across ALL the keys (for context, the HKEY_LOCAL_MACHINE or the HKEY_USERS is a key) in your Windows Registry.
<br>
<img src="https://github.com/user-attachments/assets/d8cb1576-9429-4de1-b816-afab33d84e05" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<div align="center">
List of all the hives across all the keys in the Windows Registry
</div>

Another thing which catches our attention is the word **Hypervisor**. Now for those who don't know, hypervisor is nothing but a software whose responsibility is to manage the virtual machine. It manages the system's resources (such as RAM, storage, CPU cores) between the host and the virtual machine(s). Let us see what is there in store for us.
<br>
<img src="https://github.com/user-attachments/assets/4886304b-0828-4fc8-bf3e-e9a4ea314de3" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<div align="center">
We can see 'HypervisorLaunchType' and 'IgnoreMemPart'. Let us understand these.
</div>

- **HypervisorLaunchType** - The value of this field implies whether to keep the hypervisor on or off when the system starts. It can have 3 values - 0 (off), 1(on if virtualisation-related features are active) or 2(unconditional on).

- **IgnoreMemPart** - Ok so let us understand a concept before getting into this field. In old school systems, a part of RAM was fixed for the virtualisation softwares. That is static allocation. However, nowadays RAM is dynamically allocated and this is where IgnoreMemPart comes in. If the value is set to 1 then the RAM reserved for virtualisation is freed up. Setting it to 0 means that RAM is always reserved (think of how array size was fixed but linked list size varied in Data Structures in C - same analogy).

Moving on, we see another folder - **File Systems**. Now a file system is basically any method which an OS uses to manage, name and store its files. File systems also determine how data can be retrieved in a given OS. Windows uses NTFS file system so when we go into this folder we expect parameters related to NTFS.
<img src="https://github.com/user-attachments/assets/fe91af84-5aeb-4e29-afe3-98bbba581a3a" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<div align="center">
As expected, we see a series of REG_QWORD and REG_DWORD values
</div>
The settings determine how the file system will behave thus affecting how the operating system manages the files we use everyday. For instance, note that the 'LongPathsEnabled' has been set to 0. 'LongPathsEnabled' is used to determine whether applications can access file paths longer than 260 characters. Setting it to 0 implies that the apps cannot access paths more than 260 characters. Also look at NtfsDisableLastAccessUpdate. Turning this on means Windows will not update the last accessed timestamps for your files. This prevents unnecessary write commands to the HDD or SSD.

Now we see another folder namely **Keyboard Layout**. Now you may see two of these - one in **ControlSet001** (in which we are currently working) and in the SYSTEM hive. The difference between the two is that the one in which we are working has **all the different keyboard codes for different regions in the world** whereas the one in **SYSTEM hive** acts as a **fundamental ruleset used to remap the keyboard** (in simple terms changing the keyboard inputs such that when you press 'A' the letter 'Q' gets printed!).
Coming back to the ControlSet001 we see two folders **DosKeybCodes** and **DosKeybIDs**. So first let us understand what are these and why do we need these. Basically, the Windows in your system is a combination of the old and new versions. Sounds crazy right? But yes it's a fact. The Windows with whom we interact daily is the new version. It gets all the regular updates. However, the basic system fundamentals (like the blue screen or the startup) work on the simpler, older versions. Naturally the older version cannot understand the modern terms so it will struggle to communicate with the newer one. Therefore these codes exist to act as a bridge between the two versions.

<img src="https://github.com/user-attachments/assets/caefafc8-6bf7-4d55-8109-45aeeb5b8d9d" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<div align="center">
The Keyboard Layout folder inside the ControlSet001. Note another folder here Keyboard Layouts. We will come to it later.
</div>

We may get another question here. There are DosKeybCodes for different languages. Why so? Do the different versions of Windows interact with each other in different languages? In short, no. The different language codes are due to the fact that there are multiple keyboard formats used across the world. So naturally everything cannot be based on the QWERTY keyboard layout. Windows provides all the key codes to the user even if we don't need any of those (of course except the one which we will use in our regular life - here US/UK version or the QWERTY keyboard).

<img src="https://github.com/user-attachments/assets/773bb702-57cc-430c-8ee2-4fc46e6dc81a" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<img src="https://github.com/user-attachments/assets/1756b8bf-09d7-49ac-9e95-dde1f644bd03" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<img src="https://github.com/user-attachments/assets/028c6c94-67b6-49d3-b6de-5f940617ddfb" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<div align="center">
(from top to bottom): The AZERTY (used in France and Belgium), JCUKEN (used in Russia) and the QWERTY keyboard layouts
</div>
<img src="https://github.com/user-attachments/assets/c1eb1ee4-8a4e-4602-8e9b-78392c99689a" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<div align="center">
The different keyboard codes in DosKeybCodes
</div>

Now notice the DosKeybIDs? This is present for those countries which have multiple keyboard layouts. For instance, Turkiye has two keyboard layouts - the Turkish Q and the Turkish F. Therefore we have IDs 179 and 440 for them respectively. Similarly Italy has 141 and 142 for its variants and Greece has been assigned 220 and 319.

<img src="https://github.com/user-attachments/assets/3ea54c2c-1013-42db-8144-bf1715cb6790" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<img src="https://github.com/user-attachments/assets/45a58115-3355-4e6a-a7be-d109241148ca" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<div align="center">
(from top to bottom): The Turkish Q and Turkish F keyboard layouts
</div>

<img src="https://github.com/user-attachments/assets/8c3da1d9-8fa9-4e8f-8f01-b39f0897c7a0" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<div align="center">
The keyboard layout IDs for multiple layouts in a single language
</div>

Remember we noticed **Keyboard Layouts**? Well Keyboard Layouts is a library of all the languages which your Windows keyboard can let you type. In case you want to add a keyboard and type in Portuguese for instance then Windows will check this sub-folder and if the language is present will add a keyboard for you.

<img src="https://github.com/user-attachments/assets/0939f51e-7189-4419-b2ea-ff9a892e343c" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<div align="center">
Windows provides nearly 200 languages or its variations to add as a keyboard.
</div>

Now we come out of the ControlSet001 to explore more. 

Now we see whether the user had attached any external drives to this system. For this we need to go to the **Mounted Devices** folder. 
<img src="https://github.com/user-attachments/assets/5a5d959f-5c4b-4723-969a-fbfd051ccc48" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<div align="center">
List of all the storage drives connected to my system.
</div>
We see a full list of data which we presume to be USB drives. However that is not the case. See the \??\Volume{}? This is the identifier for a single USB drive. Just the way we identify USB drives with their names (the very famous CruzerBlade for SanDisks), same way Windows uses Volume GUIDs to identify them. These GUIDs are maintained for a USB as long as the drive is not formatted. For instance \??\Volume{8abcd} is the identifier given to my SanDisk pen drive by my laptop. So as long as my laptop's drive is not formatted this GUID is maintained by my Windows and even if I plug in the same USB after 10 years my OS will easily identify it. Now we can also find the manufacturer from the values provided here.

<img src="https://github.com/user-attachments/assets/6350f5bf-1d8f-468a-ab02-ff8db8234ece" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<div align="center">
Observe the red box. It says - 'USBSTOR#Ven_Generic_Prod_Flash_Disk'. This implies that the USB I plugged in is a random one and not from any known manufacturer (like SanDisk or HP).
</div>

5\. **Conclusions** - This report may not have been as investigative as the previous ones but it is very important to understand how the system is managing the hardware in and around it. In fact, the MountedDevices folder will be a target for investigators who are trying to understand how a malware infected a given system.
   
6\. **Easter Eggs** - One thing which should be kept in mind is that the MountedDevices folder keeps record of only storage devices. Any network dongle or drive of a wireless keyboard/mouse will not be recorded here. For that, we have to visit:

```dos
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Enum\USB
```
or
```dos
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Enum\HID
```

ii. We can also find more information on the USB drive which we have connected by visiting:
```dos
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Enum\USBSTOR
```
<img src="https://github.com/user-attachments/assets/887cbe2b-d11d-4bbd-9797-3309c54099fe" style="max-width: 100%; height: auto !important; display: block;" alt="Registry Forensic ShellBags Screenshot" />
<div align="center">
This is some further information on my Generic Flash Disk. Note two entries under the same firmware version (Rev 8.07). This means that I had attached two USBs of the same controller model to the system, which is true since I do have two generic pen drives of the exact same type. Further note the 'Linux File-Stor Gadget' entry, which implies that either a Raspberry PI or an Android phone was connected to the system. Again this record can also be verified by me as I had connected my phone to the system using a USB cable a few days ago. 
</div>

7\. **Next Session** - Exploring HKEY_LOCAL_MACHINE further.
   






