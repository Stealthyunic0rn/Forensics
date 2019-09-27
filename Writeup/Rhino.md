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
    
    
    
IPs 137.30.122.253
137.30.120.40 .39
67.72.8.94
12.158.80.10
128.138.182.127


Rhinolog1
login:ggnnoommee
Password: gnome123
Last login: Mon Apr 26 16:47:47 from 137.30.122.253

Enter existing login password: gnome123
New Password: gnome12345
Permission Denied

Frame 1841: 1514 bytes on wire (12112 bits), 1514 bytes captured (12112 bits)
Ethernet II, Src: Apple_cc:57:92 (00:03:93:cc:57:92), Dst: Oracle_f0:13:96 (08:00:20:f0:13:96)
Internet Protocol Version 4, Src: 137.30.122.253, Dst: 137.30.120.40
Transmission Control Protocol, Src Port: 1662, Dst Port: 20, Seq: 90521, Ack: 1, Len: 1460
FTP Data (1460 bytes data)
[Setup frame: 1761]
[Setup method: PORT]
[Command: STOR rhino3.jpg]
Command frame: 1763
[Current working directory: ]

total 264
-rwxr-xr-x   1 gnome    cscistu     2307 Feb 26 18:26 Xinitrc.XFce
-rw-r--r--   1 gnome    cscistu      269 Feb 26 17:17 cshrc.user
-rw-r--r--   1 gnome    cscistu   117773 Apr 21 16:41 golden.jpg

.253
FTP rhino1.jpg
contraband.zip

Rhinolog2

http://www/cs/uno/edu
with syllabus

Rhino3
from 137.30.123.234
href="http://groups.google.com/groups?q=rhino.exe


Photorec
8 pictures of reptiles

1 Doc f0335017_She_died_in_February_at_the_age_of_74
Reading the document appears to be a blog or diary.

"Rhino pictures illegal?  Makes me sick.  I “hid” the photos…hehehehe.  Apparently, if there are less than 10 photos, it’s no big deal."

"OK.  Things are getting a little weird.  I zapped the hard drive and then threw it into the Mississippi River.  I’m gonna reformat my USB key after this entry, but try not to destroy the good stuff.  I need to change the password on the gnome account that Jeremy gave me.  I can probably just do that at Radio Shack."




 



 

    
  
