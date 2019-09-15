# Jackcf difr challenge Background

So this challenged comes with evidence from 4 machines and a pcap file from the 172.16.150.0/24 network.
The objective of this challenge is to 
1. Determine compromised machines.  
	a. evidence files were collected from machines suspected to be involved in this incident
	
2. Identify the who, what, when and how

3. Make a determination based on the evidence provided if you 
believe this incident is fully contained, or are there additional machines that you believe may still be involved.

## Start
Using volatility was the first step in order to collect information on the images. **imageinfo** was used to find out the OS
