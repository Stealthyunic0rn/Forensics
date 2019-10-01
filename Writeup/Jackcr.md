
# Jackcf difr challenge Background

This challenge comes with evidence from 4 machines and a pcap file from the 172.16.150.0/24 network.
The objective of this challenge is to 
1. Determine compromised machines.  
	a. evidence files were collected from machines suspected to be involved in this incident
2. Identify the who, what, when and how
3. Make a determination based on the evidence provided if you 
believe this incident is fully contained, or are there additional machines that you believe may still be involved.

## Memory Analysis
First, I wanted to get as much information as possible on the different images. Using both **imageinfo** and the **connections** command gives me the profile, time the image was taken, and the IP of that NIC. 

DC-USTXHOU: Win2003SP0x86 172.16.150.10 UTC+0000
           Image date and time : 2012-11-27 02:01:57 UTC+0000

ENG-USTXHOU-148: WinXPSP2x86 172.16.150.20 UTC+0000
           Image date and time : 2012-11-27 01:57:28 UTC+0000

FLD-SARIYADH-43: WinXPSP2x86 172.16.223.187 UTC +0300
           Image date and time : 2012-11-27 01:46:00 UTC+0000

IIS-SARIYADH-03: Win2003SP0x86 172.16.223.47 UTC +300
           Image date and time : 2012-11-27 01:52:37 UTC+0000

## Packet Analysis
The PCAP has around two hours of traffic (11:01 to around 1:35) 
The ports were 1097, 1098, 1099, 80, and other TCP ports. The 1097-99 ports are commonly used for malware, especially RATs.
Looking at the IP's, I can determine that only two IPs are talking, ENG-USTXHOU-148 (172.16.150.20) and 58.64.132.141.
After inspecting and follow some of the traffic a word "Gh0st" keeps appearing. Googling, it turns out to be a RAT.

## ENG-USTXHOU-148
Since we know that one of the IPs is one of our images, I decided to dig a bit deeper in this specific memory dump. Since we already know the imageinfo, I can start **pstree**  right off the bat. The output looked pretty normal but what stood out to me was the times of the processes. 
**wc, cmd, and mdd** all started way after the normal process did.
		
	..... 0x82049690:wc.exe                               364   1024      1     27 2012-11-27 01:30:00 UTC+0000

	... 0x82244020:lsass.exe                              692    628     22    407 2012-11-26 22:03:30 UTC+0000
	.. 0x821b0020:csrss.exe                               604    356     12    351 2012-11-26 22:03:29 UTC+0000
	 0x8204f020:explorer.exe                              284    244      9    372 2012-11-26 22:03:58 UTC+0000
	. 0x82226650:msmsgs.exe                               548    284      3    204 2012-11-26 22:04:03 UTC+0000
	. 0x822d0828:cmd.exe                                 1796    284      1     33 2012-11-27 01:56:21 UTC+0000
	.. 0x820b13b8:mdd.exe                                 244   1796      1     24 2012-11-27 01:57:28 UTC+0000
	
After I ran the **sockets** command and the time of one process stood out, which lead me to follow and diver deeper on that process.
	
	Offset(V)       PID   Port  Proto Protocol        Address         Create Time	
	0x8204d008     1024   1291      6 TCP             0.0.0.0         2012-11-27 01:56:30 UTC+0000

Diggin into the **1024** process I wanted to check all the DLLs that were loaded on this guy. After a lot of comparing and contrasting (thanks Google), a DLL named *6to4ex.dll** may be potentially malicious.

	0x10000000    0x1c000        0x1 c:\windows\system32\6to4ex.dll
 
 Since we have a potential DLL that may be malicious, its time to further analyze it with **DLLdump**. Using the **strings** and **grep** command I found some clues. 

	$ strings module.1024.20b3da0.10000000.dll | grep -i Gh0st
	Gh0st Update
	Global\Gh0st %d
	e:\gh0st\server\sys\i386\RESSDT.pdb

This system definitely got infected
I used **iehistory** and **cmdscan**. It looks like the user may have accidentally clicked on a link that lead to a **Symantec-1.43-1.exe**. The output of cmdscan shows a user opening a share called *ITSHARE*on one of the servers (DC-USTXHOU) and performing a memory dump.

	$ vol.py -f memdump.bin iehistory
	Volatility Foundation Volatility Framework 2.6
	**************************************************
	Process: 284 explorer.exe
	Cache type "DEST" at 0xdcb69
	Last modified: 2012-11-26 17:01:53 UTC+0000
	Last accessed: 2012-11-26 23:01:54 UTC+0000
	URL: callb@http://58.64.132.8/download/Symantec-1.43-1.exe

	CommandHistory: 0x11486f8 Application: cmd.exe Flags: Allocated, Reset
	CommandCount: 5 LastAdded: 4 LastDisplayed: 4
	FirstCommand: 0 CommandCountMax: 50
	ProcessHandle: 0x348
	Cmd #0 @ 0x4f2f38: net use r: \\172.16.150.10\ITShare
	Cmd #1 @ 0x4f1f68: cd\
	Cmd #2 @ 0x4f32a0: copy r:\mdd.exe .
	Cmd #3 @ 0x4f2720: dir
	Cmd #4 @ 0x4f2e98: mdd.exe -o callb-memdump.bin

So we have command history, a malicious link but still no origin. I ran the strings command with that external C2 IP (58.64.132.141). I get what looks like an SMTP header and wanted to see if there was any context behind it. 
	
	strings memdump.bin | grep 58.64.132.141
	Received: from d0793h (d0793h.petro-markets.info [58.64.132.141])
	from d0793h (d0793h.petro-markets.info [58.64.132.141]) by ubuntu-router (8.14.3/8.14.3/Debian-9.2ubuntu1) with SMTP id qAQK06Co005842; Mon, 26 Nov 2012 15:00:07 -0500

After adding 25 lines before and after this string (d0793h.petro-markets.info ) I get the body of a phishing email. From this, I know now the sender and who it was sent too.


	strings memdump.bin | grep -C 25 d0793h.petro-markets.info

	Received: from d0793h (d0793h.petro-markets.info [58.64.132.141])
	by ubuntu-router (8.14.3/8.14.3/Debian-9.2ubuntu1) with SMTP id qAQK06Co005842;
	Mon, 26 Nov 2012 15:00:07 -0500
	Message-ID: <FCE1C36C7BBC46AFB7C2A251EA868B8B@d0793h>
	From: "Security Department" <isd@petro-markets.info>
	To: <amirs@petro-market.org>, <callb@petro-market.org>,
        <wrightd@petro-market.org>
	Subject: Immediate Action
	Date: Mon, 26 Nov 2012 14:59:38 -0500
	MIME-Version: 1.0
	Content-Type: multipart/alternative;
		boundary="----=_NextPart_000_0015_01CDCBE6.A7B92DE0"
	X-Priority: 3
	X-MSMail-Priority: Normal
	X-Mailer: Microsoft Outlook Express 6.00.2900.5512
	X-MimeOLE: Produced By Microsoft MimeOLE V6.00.2900.5512
	Return-Path: isd@petro-markets.info
	X-OriginalArrivalTime: 26 Nov 2012 20:00:08.0432 (UTC) FILETIME=[A2ABBF00:01CDCC10]
	This is a multi-part message in MIME format.
	------=_NextPart_000_0015_01CDCBE6.A7B92DE0	Content-Type: text/plain;
	Content-Type: text/plain;
		charset="iso-8859-1"
	Content-Transfer-Encoding: quoted-printable
	Attn: Immediate Action is Required!!
	The IS department is requiring that all associates update to the new =
	version of anti-virus.  This is critical and must be done ASAP!  Failure =

Lastly, I wanted the hash of the process 6to4ex. I used **procdump** to turn it into an executable and checking the MD5 hash on VirusTotal returns positive.

	$ md5sum module.1024.20b3da0.10000000.dll
	156f2c6a65a1eab1c03e1dc7f215a044  	module.1024.20b3da0.10000000.dll

## DC
Going back to our cmdscan output, I decided to check this machine next. I ran pstree and sockets, but not much came up. Two processes caught my eye cause of the time but those led to nothing. No signs of Gh0st, the malicious DLL, or that C2 IP. It appears that this machine was not compromised.

## FLD
We run **connscan** and notice the C2 server talking with this machine. The process associated with this is *1032, svchost.exe.
	
	0x02023638 172.16.223.187:1265       58.64.132.141:80          1032

Time to check the DLLs of this one. I did the same with the last machine and found our malicious DLL. I ran strings against the dumped module to check for "Gh0st" and got results. I also got back the same phishing email which tells me that this machine also was infected.

	0x10000000    0x1c000        0x1 c:\windows\system32\6to4ex.dll
	
	strings module.1032.228fda0.10000000.dll | grep -i "Gh0st"
	Gh0st Update
	Global\Gh0st %d
	e:\gh0st\server\sys\i386\RESSDT.pdb	

Just out of curiosity I check the IE history of the machine and the results show that malicious link.	
	
	URL: amirs@http://58.64.132.8/download/Symantec-1.43-1.exe

## IIS 
The last machine to analyze and going through our routine does not appear to be much. No sign of Gh0st, the email, or the C2 IP. There is, however, a process *PSEXESVC.exe** which is used fo rremotely executing processes on other machines. 

	.... 0x81f7f2b0:PSEXESVC.EXE                          268    528      4     85 2012-11-27 00:05:49 UTC+0000

This machine was probably targeted, and files were taken from here, but it does not appear the machine itself is infected.

To recap, at this point we managed to find
1.  C2 server IP (58.64.122.141)
2. The malicious DLL 6to4ex.dll which was loaded into svchost.exe
3. The DLL MD5 hash  156f2c6a65a1eab1c03e1dc7f215a044  
4. The Gh0st string in the memory image
5. A potentially malicious link /download/Symantec-1.43-1.exe
6. A phishing email with the sender and receivers with the domain
7. Our attack window is between 23:01:54 UTC, which is the time of the first backdoor executing and 02:01:56 UTC, which is the last memory capture (DC).
8. 2 out of 4 Machines are infected with the Gh0st malware

# Questions

1. Who delivered the attack? "Security Department" <isd@petro-markets.info>
2. Who was the attack delivered too? <amirs@petro-market.org>, <callb@petro-market.org>,
        <wrightd@petro-market.org>
3. What time was the attack delivered? Date: Mon, 26 Nov 2012 14:59:38 -0500
4. What time was the attack executed?
5. What is the C2 IP Address? 58.64.132.141
6. What is the name of the dropper? Symantec-1.43-1.exe
7. What is the name of the backdoor? Gh0st
8. What is the process name the backdoor is running in? 6to4ex.dll
9. What is the process id on all the machines the backdoor is installed on? ENG (PID 1024), FLD (PID 1032) 
10. What usernames were used in this attack? 
11. What level of access did the attacker have?
12. How was lateral movement performed?
13. What .bat scripts were placed on the machines?
14. What are the contents of each .bat script?
15. What other tools were placed on the machines by the attacker?
16. What directory was used by the attacker to drop tools?
17. Was the directory newly created or was it there prior to the attack?
18. What were the names of the exfiltrated files?
19. What did the exfiltrated files contain? 
20. What time did winrar run?
21. What is the md5sum of pump1.dwg?
22. Which machines were compromised and need to be remediated?
23. Which user accounts were compromised and need to be remediated?
24. Are there additional machines that need to be analyzed?
25. Describe how each machine was involved in this incident and overall what happened
