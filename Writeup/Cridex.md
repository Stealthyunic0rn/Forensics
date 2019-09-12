#Cridex Writeup

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

We choose the first profile **WinXPSP2x86** and run the **pstree** command to see more of a heighracy view of the processes.
What caught my eye was that two of the processes (1484 1640) which were on their own level in the tree. 
What also made them stand out was checking out the connscan output, which showed us the two suspected processes were connected to 
an external host over port 8080.

        0x02087620 172.16.112.128:1038       41.168.5.140:8080         1484
        0x023a8008 172.16.112.128:1037       125.19.103.198:8080       1484
        
I then ran malfind which malfind shows the area of memory marked as executable with no mapped file on disk 
for that particular section of memory which is highly suspect. Their was an MZ headers which means that this a 
full Win32 executable that has been injected into this memory region.

Vaddump
Strings
apihooks
procdump
compare hash, virus total
