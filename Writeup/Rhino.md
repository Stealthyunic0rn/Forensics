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
The first action was checking out the log files; when analyzing the first log file, I found potential login credentials for a system. The user was also trying to change his credentials but got denied access.

    Login:ggnnoommee
    Password: gnome123
    Last login: Mon Apr 26 16:47:47 from 137.30.122.253

    Enter existing login password: gnome123
    New Password: gnome12345
    Permission Denied
    
  There were also different files on the system, and when inspecting the FTP traffic, it appears that media was transferred over. All through the 137.30.122.25.
  
    Total 264
    -rwxr-xr-x   1 gnome    cscistu     2307 Feb 26 18:26 Xinitrc.XFce
    -rw-r--r--   1 gnome    cscistu      269 Feb 26 17:17 cshrc.user
    -rw-r--r--   1 gnome    cscistu   117773 Apr 21 16:41 golden.jpg
 
    [Command: STOR rhino3.jpg] 
    [Command: STOR rhino1.jpg] 
    [Command: STOR contraband.zip] 
    
 
## Rhinolog 2

I found in this log file was a site the user kept visiting. It turns out to be an academic site with a class syllabus. There are additional images that have been transferred. The photos have been downloaded to a machine with IP 137.30.123.234 from a machine with IP 137.30.120.37 and hostname www.cs.uno.edu

    http://www/cs/uno/edu
  
 
 ## Rhinolog 3
Looking at this log file their appears to be a .exe file taken from a google groups account coming from the 137.30.123.234 IP.
     
    href="http://groups.google.com/groups?q=rhino.exe 
    
## Analysis on DD image
The first thing that came to mind was running autopsy on this guy and see what I can find, nothing. So I ran a program called Photorec which recovers media from disk images. The results were eight pictures of reptiles and a few documents with the name "Charlie" and a gumbo recipe. There was, however, one document that looked like a diary. After reading it, it turns out that this is a diary and the user tried getting rid of the evidence by throwing the USB in the Mississippi River.

    Doc f0335017_She_died_in_February_at_the_age_of_74

    "Rhino pictures illegal?  Makes me sick.  I “hid” the photos…hehehehe.  Apparently, if there are less than 10 photos, it’s no big deal."

    "OK.  Things are getting a little weird.  I zapped the hard drive and then threw it into the Mississippi River.  I’m gonna reformat my USB key after this entry, but try not to destroy the good stuff.  I need to change the password on the gnome account that Jeremy gave me.  I can probably just do that at Radio Shack."
    
    
    
## Questions
1. Who gave the accused a telnet/ftp account? **Jeremy**
2. What’s the username/password for the account? **Login:ggnnoommee Password: gnome123**
3. What relevant file transfers appear in the network traces? **Images of Rhinos**
4. What happened to the hard drive in the computer? Where is it now? **User was freaked out and decided to throw the image into the Missippi River**
5. What happened to the USB key? **The user reformated the image but kept the "good stuff".**
6. What is recoverable from the dd image of the USB key? **Rhino images and different text documents includind a diary and recipes for gumbo.
7. Is there any evidence that connects the USB key and the network traces? If so, what? **rhino2.jpg carved from USBKEY is same as rhino2.jpg in zip file from network trace** 
    





 



 

    
  
