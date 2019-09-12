# Art of Memory Forensics
 *All of these questions are part of the Art of Memory Forensics book*
## Lab 1
*The winlogon.exe process (PID 628) in sample001.bin has a virtual address 0x77a80000 and DTB value 0x682e000. What is the corresponding physical offset? What do you see at the physical offset within the file?*

**The winlogon.exe process (PID 628) in sample001.bin has a virtual address 0x77a80000 and DTB value 0x682e000. 
What is the corresponding physical offset? What do you see at the physical offset within the file?**

First we run volatility: **vol.py -f sample001.bin volshell**

Volshell lets us interactively explore a memory image using some of volshells built in commands. 

**cc(pid=628)** lets us change the current shell to this pid. 
Our output is **Current context: winlogon.exe @ 0x82189da0, pid=628, ppid=356 DTB=0x682e000**
So we now have set this pocess as out context which is necessary for the next steps.

We run **proc().get_process_address_space().vtop(0x77a80000)** 

Breaking that down
**proc().get_process_address_space()** gets the address space for the current process address space
, **vtop** is needed to translate virtual adreess to physical offsets. 

Our results are **72159232** which is our physical offset.

Now we want to see whats at that offset. To do that we use **xxd** which allows us to create hexdumps or even do the reverse of a file.
**xxd -s 72159232 sample001.bin | more**. We can now view the contents in that offset which were "This program cannot be run is DOS mode"
