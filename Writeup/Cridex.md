# Cridex Writeup

Running volatility we first check the imageinfo by running **volatility -f cridex.vmem imageinfo**

Our output is:

          Volatility Foundation Volatility Framework 2.6
          INFO    : volatility.debug    : Determining profile based on KDBG search...
          Suggested Profile(s) : WinXPSP2x86, WinXPSP3x86 (Instantiated with WinXPSP2x86)
          AS Layer1 : IA32PagedMemoryPae (Kernel AS)
                     AS Layer2 : FileAddressSpace (/root/Downloads/cridex.vmem)
                      PAE type : PAE
          DTB : 0x2fe000L
                          KDBG : 0x80545ae0L
          Number of Processors : 1
     Image Type (Service Pack) : 3
                KPCR for CPU 0 : 0xffdff000L
             KUSER_SHARED_DATA : 0xffdf0000L
           Image date and time : 2012-07-22 02:45:08 UTC+0000
     Image local date and time : 2012-07-21 22:45:08 -0400 

Choosing the first profile **WinXPSP2x86** and run the **pstree** command to see more of a heighracy view of the processes.
What caught my eye was that two of the processes (1484 1640) which were on their own level in the tree. 
What also made them stand out was checking out the connscan output, which showed us the two suspected processes were connected to an external host over port 8080.

        0x02087620 172.16.112.128:1038       41.168.5.140:8080         1484
        0x023a8008 172.16.112.128:1037       125.19.103.198:8080       1484
        
I then ran **malfind** which malfind shows the area of memory marked as executable with no mapped file on disk 
for that particular section of memory which is highly suspect. Their was an MZ headers which means that this a 
full Win32 executable that has been injected into this memory region.

          Process: explorer.exe Pid: 1484 Address: 0x1460000
          Vad Tag: VadS Protection: PAGE_EXECUTE_READWRITE
          Flags: CommitCharge: 33, MemCommit: 1, PrivateMemory: 1, Protection: 6

          0x01460000  4d 5a 90 00 03 00 00 00 04 00 00 00 ff ff 00 00   MZ..............
          0x01460010  b8 00 00 00 00 00 00 00 40 00 00 00 00 00 00 00   ........@.......
          0x01460020  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
          0x01460030  00 00 00 00 00 00 00 00 00 00 00 00 e0 00 00 00   ................

          Process: reader_sl.exe Pid: 1640 Address: 0x3d0000
          Vad Tag: VadS Protection: PAGE_EXECUTE_READWRITE
          Flags: CommitCharge: 33, MemCommit: 1, PrivateMemory: 1, Protection: 6

          0x003d0000  4d 5a 90 00 03 00 00 00 04 00 00 00 ff ff 00 00   MZ..............
          0x003d0010  b8 00 00 00 00 00 00 00 40 00 00 00 00 00 00 00   ........@.......
          0x003d0020  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
          0x003d0030  00 00 00 00 00 00 00 00 00 00 00 00 e0 00 00 00   ................


**Vaddump** allowed me to furuter analyze the memory regions of both processes. to see if the malware was using certain commands.

          volatility -f cridex.vmem vaddump -p 1484 --dump-dir /root/Downloads/vad

I used the **strings** command to view human readable characters in our .dmp file. We can see certain API calls were used to inject the payload. VirtualAllocEx reserves, commits, or changes the state of a region of memory within the virtual address space of a specified process. WriteProcessMemory writes data to an area of memory in a specified process. The new malicious thread of execution was spawned via CreateRemoteThread.

          strings explorer.exe.23dea70.0x01460000-0x01480fff.dmp 

Then running the **apihooks** allowed us to find API/DLL function hooks for the specific processes. The hook types are inline/trampoline which certain malware uses in order to modify data returned back from system calls. The malware hooks functions are secur32.dll, ntdll.dll, and ws_32.dll.  The functions hooked from ws_32.dll are all socket-related, likely the source of the connections over Port 8080.  The secur32.dll functions that are hooked are all related to encrypting/decrypting data in transit.  This is likely the data being sent/received via the Port 8080 socket.  The functions hooked from ntdll.dll are used to load a module and resume the thread of execution of the injected code.

          Hook mode: Usermode
          Hook type: Inline/Trampoline
          Process: 1484 (explorer.exe)
          Victim module: ntdll.dll (0x7c900000 - 0x7c9af000)
          Function: ntdll.dll!LdrLoadDll at 0x7c9163a3
          Hook address: 0x146a300
          Hooking module: <unknown>
          
          Hook mode: Usermode
          Hook type: Inline/Trampoline
          Process: 1484 (explorer.exe)
          Victim module: ntdll.dll (0x7c900000 - 0x7c9af000)
          Function: ntdll.dll!NtResumeThread at 0x7c90db20
          Hook address: 0x146a330
          Hooking module: <unknown>
          
          Hook mode: Usermode
          Hook type: Inline/Trampoline
          Process: 1484 (explorer.exe)
          Victim module: Secur32.dll (0x77fe0000 - 0x77ff1000)
          Function: Secur32.dll!DecryptMessage at 0x77fea64a
          Hook address: 0x1469f20
          Hooking module: <unknown>
          
          Hook mode: Usermode
          Hook type: Inline/Trampoline
          Process: 1484 (explorer.exe)
          Victim module: WS2_32.dll (0x71ab0000 - 0x71ac7000)
          Function: WS2_32.dll!connect at 0x71ab4a07
          Hook address: 0x1467250
          Hooking module: <unknown>

**Userassist** displays a table of programs executed on a Windows machine, complete with the running count and last execution date and time. Looking at the output I noticed the only executable on the list, "cridex1.exe". When looking up that .exe file it resulted in malware. According to my searches "Cridex is a sophisticated strain of banking malware that can steal banking credentials and other personal information on an infected system in order to gain access to the financial records of a user." 
          
          REG_BINARY    UEME_RUNPATH:C:\WINDOWS\cridex1.exe : 
          ID:             3
          Count:          1
          Last updated:   2012-07-22 02:31:51 UTC+0000
          Raw Data:
          0x00000000  03 00 00 00 06 00 00 00 00 a1 9a 26 b2 67 cd 01   ...........&.g..



I've decided to run **procdump** to make the processess an executable so I can take the hash and compare it.
compare the hash on virus total. 

          volatility --profile=WinXPSP2x86 -f cridex.vmem procdump -p 1484,1640 --dump-dir=/root/Downloads/exe
          
          Volatility Foundation Volatility Framework 2.6
          Process(V) ImageBase  Name                 Result
          ---------- ---------- -------------------- ------
          0x821dea70 0x01000000 explorer.exe         OK: executable.1484.exe
          0x81e7bda0 0x00400000 reader_sl.exe        OK: executable.1640.exe

          md5sum executable.1484.exe 
          
          f5d61a0ccf96e07228a4818918aa33e8  executable.1484.exe
          
As a result, we get back a trojan results from the hash.



