
#### What is Memory Forensics?

Memory forensics is the analysis of the volatile memory that is in use when a computer is powered on. Computers use dedicated storage devices called Random Access Memory (RAM) to remember what is being performed on the computer at the time. RAM is extremely quick and is the preferred method of storing and accessing data. However, it is limited compared to storage devices such as hard drives. This type of data is volatile because it will be deleted when the computer is powered off. RAM stores data such as your clipboard or unsaved files.

We can analyse a computer's memory to see what applications (processes), what network connections were being made, and many more useful pieces of information. For example, we can analyse the memory of a computer infected with malware to see what the malware was doing at the time.

Let's think about cooking. You normally store all of your food in the fridge - a hard drive is this fridge. When you are cooking, you will store ingredients on the kitchen counter so that you can quickly access them, but the kitchen counter (RAM) is much smaller than a fridge (hard drive)

#### Why is Memory Forensics Useful?

Memory forensics is an extremely important element when investigating a computer. A memory dump is a full capture of what was happening on the Computer at the time, for example, network connections or things running in the background. Most of the time, malicious code attempts to hide from the user. However, it cannot hide from memory.

We can use this capture of the memory for analysis at a later date, especially as the memory on the computer will eventually be lost (if, for example, we power off the computer to prevent malware from spreading). By analysing the memory, we can discover exactly what the malware was doing, who it was contacting, and such forth.

#### An Introduction to Processes

At the simplest, a process is a running program. For example, a process is created when running an instance of notepad. You can have multiple processes for an application (for example, running three instances of notepad will create three processes). This is important to know because being able to determine what processes were running on the computer will tell us what applications were running at the time of the capture.

On Windows, we can use Task Manager_(pictured below)_ to view and manage the processes running on the computer.

![A picture of Window's Task Manager](https://tryhackme-images.s3.amazonaws.com/user-uploads/5de96d9ca744773ea7ef8c00/room-content/68217febd77dcd845ba608bb5ef6f34f.png)

_Window's Task Manager_

On a computer, processes are usually categorised into two groups:

|**Category**|**Description**|**Example**|
|----|-----|----|
|User Process|These processes are programs that the user has launched. For example, text editors, web browsers, etc.|notepad.exe - this is a text editor that is launched by the user.|
|Background Process|These processes are automatically launched and managed by the Operating System and are often essential to the Operating System behaving correctly.|dwm.exe - this is an essential process for Windows that is responsible for displaying windows and applications on the computer.|

#### Introducing Volatility  

Volatility is an open-source memory forensics toolkit written in Python. Volatility allows us to analyse memory dumps taken from Windows, Linux and Mac OS devices and is an extremely popular tool in memory forensics. For example, Volatility allows us to:

-   List all processes that were running on the device at the time of the capture
-   List active and closed network connections
-   Use Yara rules to search for indicators of malware
-   Retrieve hashed passwords, clipboard contents, and contents of the command prompt
-   And much, much more!

Once Volatility and its requirements (i.e. Python) are installed, Volatility can be run using `python3 vol.py`. The terminal below displays Volatility's help menu:

|Option|Description|Example|
|---|---|---|
|-f|This argument is where you provide the name and location of the memory dump that you wish to analyse.|`python3 vol.py -f /path/to/my/memorydump.vmem`
|-v|This argument increases the verbosity of Volatility. This is sometimes useful to understand what Volatility is doing in cases of debugging.|`python3 vol.py -v`   
|-p|This argument allows you to override the default location of where plugins are stored.|`python3 vol.py -p /path/to/my/custom/plugins`  
|-o|This argument allows you to specify where extracted processes or DLLs are stored.|`python3 vol.py -o /output/extracted/files/here`  

And finally, now we need to decide what we want to analyse the image for. Volatility uses plugins to perform analysis, such as:

-   Listing processes
-   Listing network connections
-   Listing contents of the clipboard, notepad, or command prompt
-   And much more! If you're curious, you can read the documentation [here](https://volatility3.readthedocs.io/en/latest/volatility3.plugins.html)

In this task, we are going to use Volatility to:

1.  See what Operating System the memory dump is from
2.  See what processes were running at the time of capture
3.  See what connections were being made at the time of capture
----
