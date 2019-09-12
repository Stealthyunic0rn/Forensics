# Zapftis

First checking the imageinfo of the .vnem file.

    Volatility Foundation Volatility Framework 2.6
    INFO    : volatility.debug    : Determining profile based on KDBG search...
          Suggested Profile(s) : WinXPSP2x86, WinXPSP3x86 (Instantiated with WinXPSP2x86)
                     AS Layer1 : IA32PagedMemoryPae (Kernel AS)
                     AS Layer2 : FileAddressSpace (/root/Downloads/Case/0zapftis.vmem)
                      PAE type : PAE
                           DTB : 0x319000L
                          KDBG : 0x80544ce0L
          Number of Processors : 1
     Image Type (Service Pack) : 2
                KPCR for CPU 0 : 0xffdff000L
             KUSER_SHARED_DATA : 0xffdf0000L
           Image date and time : 2011-10-10 17:06:54 UTC+0000
     Image local date and time : 2011-10-10 13:06:54 -0400
     
Looking at the pstree output

    Name                                                  Pid   PPid   Thds   Hnds Time
    -------------------------------------------------- ------ ------ ------ ------ ----
    0x819cc830:System                                      4      0     55    162 1970-01-01 00:00:00 UTC+0000
    . 0x81945020:smss.exe                                 536      4      3     21 2011-10-10 17:03:56 UTC+0000
    .. 0x816c6020:csrss.exe                               608    536     11    355 2011-10-10 17:03:58 UTC+0000
    .. 0x813a9020:winlogon.exe                            632    536     24    533 2011-10-10 17:03:58 UTC+0000
    ... 0x816da020:services.exe                           676    632     16    261 2011-10-10 17:03:58 UTC+0000
    .... 0x817757f0:svchost.exe                           916    676      9    217 2011-10-10 17:03:59 UTC+0000
    .... 0x81772ca8:vmacthlp.exe                          832    676      1     24 2011-10-10 17:03:59 UTC+0000
    .... 0x816c6da0:svchost.exe                           964    676     63   1058 2011-10-10 17:03:59 UTC+0000
    ..... 0x815c4da0:wscntfy.exe                         1920    964      1     27 2011-10-10 17:04:39 UTC+0000
    ..... 0x815e7be0:wuauclt.exe                          400    964      8    173 2011-10-10 17:04:46 UTC+0000
    .... 0x8167e9d0:svchost.exe                           848    676     20    194 2011-10-10 17:03:59 UTC+0000
    .... 0x81754990:VMwareService.e                      1444    676      3    145 2011-10-10 17:04:00 UTC+0000
    .... 0x8136c5a0:alg.exe                              1616    676      7     99 2011-10-10 17:04:01 UTC+0000
    .... 0x813aeda0:svchost.exe                          1148    676     12    187 2011-10-10 17:04:00 UTC+0000
    ... 0x817937e0:spoolsv.exe                          1260    676     13    140 2011-10-10 17:04:00 UTC+0000
    .... 0x815daca8:svchost.exe                          1020    676      5     58 2011-10-10 17:03:59 UTC+0000
    ... 0x813c4020:lsass.exe                              688    632     23    336 2011-10-10 17:03:58 UTC+0000
    0x813bcda0:explorer.exe                             1956   1884     18    322 2011-10-10 17:04:39 UTC+0000
    . 0x8180b478:VMwareUser.exe                           192   1956      6     83 2011-10-10 17:04:41 UTC+0000
    . 0x817a34b0:cmd.exe                                  544   1956      1     30 2011-10-10 17:06:42 UTC+0000
    . 0x816d63d0:VMwareTray.exe                           184   1956      1     28 2011-10-10 17:04:41 UTC+0000
    . 0x818233c8:reader_sl.exe                            228   1956      2     26 2011-10-10 17:04:41 UTC+0000

Running **malfind** with the PID of 1956 results in nothing returned.
Checking the connscan results, we see that the PID 1956 is communicationg with a remote address. Its all through port 1026 which has numerous malicious services. I also ran the **sockets** command but that didn't get me much. I also ran **psxview** to look for hidden processes but none came up.

        Offset(P)  Local Address             Remote Address            Pid
    ---------- ------------------------- ------------------------- ---
    0x01a25a50 0.0.0.0:1026              172.16.98.1:6666          1956

**Cmdscan** shows the commands that anyone entered through a console shell. It seems like the person was searching for a service named malware or malwar. This could signify that they were trying to verify that the malicious service was running.
       
       CommandProcess: csrss.exe Pid: 608
       CommandHistory: 0x11132d8 Application: cmd.exe Flags: Allocated, Reset
       CommandCount: 2 LastAdded: 1 LastDisplayed: 1
       FirstCommand: 0 CommandCountMax: 50
       ProcessHandle: 0x4c4
       Cmd #0 @ 0x4e1eb8: sc query malwar
       Cmd #1 @ 0x11135e8: sc query malware
       
       
The **cmdline** command lets us see the complete command which was used to launch each process. We can see that the process *Reader_sl.exe* was launched.
       
    reader_sl.exe pid:    228
    Command line : "C:\Program Files\Adobe\Reader 9.0\Reader\Reader_sl.exe"
    
I also usef the **consoles** which shows that there is a service running called malware which is type *KERNEL_DRIVER*. So we know that the service is running in kernel mode.

     C:\Documents and Settings\Administrator>sc query malware                        
                                                                                
    SERVICE_NAME: malware                                                           
        TYPE               : 1  KERNEL_DRIVER                                   
        STATE              : 4  RUNNING                                         
                                (STOPPABLE,NOT_PAUSABLE,IGNORES_SHUTDOWN)       
        WIN32_EXIT_CODE    : 0  (0x0)                                           
        SERVICE_EXIT_CODE  : 0  (0x0)                                           
        CHECKPOINT         : 0x0                                                
        WAIT_HINT          : 0x0 
 
With all this information on the process I decided to turn the process into executable so I can take the hash and check it in virus total. However, nothing came up in virus total. So I checked one of the children processes of PID 1956 which is *cmd.exe PID 544*. I got a hit on this hash which turns out to be a Trojan.
        
        8f000380134d51f34c0f1b075812cdd2  executable.228.exe
        6cee14703054e226e87a963372f767aa  executable.544.exe

  
    
 


