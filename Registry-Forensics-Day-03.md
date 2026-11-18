## Registry Forensics - Day 03
###### By - Ashutosh Sarangi

1. **Aim** : To understand the data stored in SYSTEM hive.

2. **Tools Used** : Registry Editor

3. **Theory** : So far we have not really gotten into any dedicated hive. We dealt with USRCLASS.dat and NTUSER.dat which were basically huge databases storing the user personality over the system and all the user-specific information and application history over the system. Today we go into SYSTEM hive. What does it do you ask? SYSTEM hive stores the system-level and OS-level information. It also stores basic information on the bootloader. Besides that there are records of the HID and USB devices connected to the system, the keyboard layouts based on regions and so on. Today we will understand the work of certain specific folders of this hive.
When you explore this hive you will see there are numerous folders here and you may wonder that the writer is too lazy to cover everything. But trust me getting into each and every folder and finding out and interpreting what information it stores is one crazy job. Either way, we are always open to learning new stuff so maybe some other day!

4. **Activity** - In this session we will look at some important folders in the SYSTEM hive and what their work is. So first let us get into the hive by visiting the following:

  ```dos
HKEY_LOCAL_MACHINE\SYSTEM
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
As expected, we see a series of REG_QWORD and REG_DWORD files
</div>
The settings determine how the file system will behave thus affecting how the operating system manages the files we use everyday. For instance, note that the 'LongPathsEnabled' has been set to 0. 'LongPathsEnabled' is used to determine whether applications can access file paths longer than 260 characters. Setting it to 0 implies that the apps cannot access paths more than 260 characters. Also look at NtfsDisableLastAccessUpdate. Turning this on means Windows will not update the last accessed timestamps for your files. This prevents unnecessary write commands to the HDD or SSD.

