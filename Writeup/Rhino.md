# Rhino

Scenario:

 The city of New Orleans passed a law in 2004 making possession of nine or more unique rhinoceros images a serious crime.   
 The network administrator at the University of New Orleans recently alerted police when his instance of RHINOVORE flagged 
 illegal rhino traffic. Evidence in the case includes a computer and USB key seized from one of the University’s labs.  
 Unfortunately, the computer had no hard drive. The USB key was imaged and a copy of the dd image is on the 
 CD-ROM you’ve been given.
 In addition to the USB key drive image, three network traces are also available—these were provided by the 
 network administrator and involve the machine with the missing hard drive. The suspect is the primary user of this machine, 
 who has been pursuing his Ph.D. at the University since 1972.

 
 
 ## Rhinolog1
 
  The given files are three log files Rhino, Rhino2, and Rhino3. As well as a .dd file.
  
  The first action was checking out the log files, when analyzing the first log file I found potential login credentials for a system. The user was also trying to change his creentials but was denied access.

    Login:ggnnoommee
    Password: gnome123
    Last login: Mon Apr 26 16:47:47 from 137.30.122.253

    Enter existing login password: gnome123
    New Password: gnome12345
    Permission Denied
    
  Their were also different files on the system and when inspecting the FTP traffic it appears that media was transfered over as well, all through the 137.30.122.25.
  
    Total 264
    -rwxr-xr-x   1 gnome    cscistu     2307 Feb 26 18:26 Xinitrc.XFce
    -rw-r--r--   1 gnome    cscistu      269 Feb 26 17:17 cshrc.user
    -rw-r--r--   1 gnome    cscistu   117773 Apr 21 16:41 golden.jpg
 
    [Command: STOR rhino3.jpg] 
    [Command: STOR rhino1.jpg] 
    [Command: STOR contraband.zip] 
    
    
    





 



 

    
  
