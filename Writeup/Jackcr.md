# Jackcf difr challenge Background
**CURRENTLY EDITING**

So this challenged comes with evidence from 4 machines and a pcap file from the 172.16.150.0/24 network.
The objective of this challenge is to 
1. Determine compromised machines.  
	a. evidence files were collected from machines suspected to be involved in this incident
	
2. Identify the who, what, when and how

3. Make a determination based on the evidence provided if you 
believe this incident is fully contained, or are there additional machines that you believe may still be involved.

## Memory Analysis
First I wanted to get as much information as possible on the differnet images. Using both **imageinfo** and **connections** gives me the profile, time the image was taken, and the IP of that NIC. 

DC-USTXHOU: Win2003SP0x86 172.16.150.10 UTC+0000

ENG-USTXHOU-148: WinXPSP2x86 172.16.150.20  UTC+0000

FLD-SARIYADH-43: WinXPSP2x86 172.16.223.187 UTC +0300

IIS-SARIYADH-03: Win2003SP0x86 172.16.223.47 UTC +300

## Packet Analysis
The PCAP has two hours of traffic
Ports 1097, 1098, 1099 Some Trojans use these ports
Looking at the IP's I can determine that only two IPs are talking ENG-USTXHOU-148 and a 58.64.132.141
Looking closely at the packets a word "Gh0st" keeps appearing. Googling it turns out to be a RAT.

PID 1024
pid 364, 1796, 244  Time difference 1h compared to all other processes

6to43x.dll

strings  module.1024.20b3da0.10000000.dll | grep "Gh0st"
Gh0st Update
Global\Gh0st %d

strings  memdump.bin| grep "58.64.132.141"
Received: from d0793h (d0793h.petro-markets.info [58.64.132.141])
from d0793h (d0793h.petro-markets.info [58.64.132.141]) by ubuntu-router (8.14.3/8.14.3/Debian-9.2ubuntu1) with SMTP id qAQK06Co005842; Mon, 26 Nov 2012 15:00:07 -0500

vol.py -f memdump.bin --profile=WinXPSP2x86 iehistory

md5sum module.1024.20b3da0.10000000.dll 
156f2c6a65a1eab1c03e1dc7f215a044 
Malicious on Virus Total


FLD

connscan results show us the connections to that 58.64 IP assigned to PID 1032.
1032 is svchost.exe

pstree
dlllist
cmdscan mdd.exe
iehistory
strings -i gh0st

$ md5sum module.1032.228fda0.10000000.dll
156f2c6a65a1eab1c03e1dc7f215a044  module.1032.228fda0.10000000.dll


DC

No connections to the server.
Not compromoised

IIS
No suspicious netowrk activity, donwload URL.
PSEXESVC.EXE to send files from this machine to others. (Check IP's)






## Challenge Questions

1. Who delivered the attack?
2. Who was the attack delivered too?
3. What time was the attack delivered?
4. What time was the attack executed?
5. What is the C2 ip Address?
6. What is the name of the dropper?
7. What is the name of the backdoor?
8. What is the process name the backdoor is running in?
9. What is the process id on all the machines the backdoor is installed on?
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
