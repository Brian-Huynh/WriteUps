# Lame
![Title](https://user-images.githubusercontent.com/58709386/82748772-6a57a380-9d59-11ea-8b91-5c7f8747c5ff.png)




## Recon and enumeration


     # Basic scan service/version scan
     nmap -sV 10.10.10.3
      -sV probes open ports to determine service/version info.   
![Scan](https://user-images.githubusercontent.com/58709386/82747935-c539cc80-9d52-11ea-8b85-a243ad446526.png)


     # Detailed Scan
     nmap -A -p 21,22,139,445 10.10.10.3
      -A Enable OS detection, version detection, script scanning, and traceroute
      -p Scanned the ports found above    
![Scan2](https://user-images.githubusercontent.com/58709386/82747934-c4a13600-9d52-11ea-9acc-983c47ee2df7.png)

 

Scan results:

      21 vsftpd version 2.3.4 - Allowed anonymous login
      22 OpenSSH 4.7p1
      139 Samba 3.X - 4.X
      445 Samba 3.X - 4.X

Used Enum4linux to see if I could get any useful information
![Enum4linux](https://user-images.githubusercontent.com/58709386/82747945-c79c2680-9d52-11ea-9ac6-c7279255d08d.png)
The results showed the version of SMB the machine is running.      
      
## Exploitation   
**Logging in FTP server**
 
 First I tried to login the FTP server to see if I can find useful information. 

 
 
 ![FTP Server](https://user-images.githubusercontent.com/58709386/82747929-c3700900-9d52-11ea-96e1-0e8aeb835b01.png)



 I tried searching for useful information in the FTP server, but it was empty.
 ![FTP Navigation](https://user-images.githubusercontent.com/58709386/82747928-c3700900-9d52-11ea-8e1b-d4d6bd5cf7e1.png)
 
 
 
**Using smbclient**

Next I tried going through the workgroup shares, when it asked for a password I pressed Enter and somehow that worked. 
 ![SMB](https://user-images.githubusercontent.com/58709386/82747932-c4a13600-9d52-11ea-8871-1495dd31eabf.png)
 


![SMB Navigation](https://user-images.githubusercontent.com/58709386/82747931-c4089f80-9d52-11ea-9b9b-d09c06f1103f.png)
![SMB Navigation](https://user-images.githubusercontent.com/58709386/82747930-c3700900-9d52-11ea-8d55-e69b59531ebb.png)

I was only able to access the tmp & IPC$ shares, unfortunately there was nothing useful.
 
 
 
 
**vsftpd 2.3.4**

Found a vulnerability for this version of vsftpd with metasploit, unfortunately the backdoor was removed already but I tried the exploit anyways.
 ![vsftpd](https://user-images.githubusercontent.com/58709386/82747927-c2d77280-9d52-11ea-8d4e-3061ef612f96.png)    
 ![PatchedVuln](https://user-images.githubusercontent.com/58709386/82747946-c79c2680-9d52-11ea-830a-f1be95de094a.png)

Unfortunately this exploit didn't produce a shell.

 
 
 **Samba 3.0.20**
Since the vsftpd didn't give me a shell or meterpreter session I found a metasploit module which exploits this version of samba.
 ![Samba](https://user-images.githubusercontent.com/58709386/82747944-c7039000-9d52-11ea-8581-75a5a1ccf56e.png)
 ![MSF Samba](https://user-images.githubusercontent.com/58709386/82747943-c7039000-9d52-11ea-9e25-ae4d839d2875.png)
 ![Shell](https://user-images.githubusercontent.com/58709386/82747942-c66af980-9d52-11ea-864c-91fd7e49962e.png)
Woooo!!! Got a shell with this exploit! With this shell I was able to get root acceess. 

**Navigating to the passwd & shadow files**
![shadow](https://user-images.githubusercontent.com/58709386/82747937-c5d26300-9d52-11ea-8130-0ec7468d01a2.png)
![passwd](https://user-images.githubusercontent.com/58709386/82747936-c539cc80-9d52-11ea-9b47-cab1c61c3613.png)

Once you get root access to a machine, the hashes are stored in two seperate files which you have to combine to start cracking the hashes.
 - shadow
 - passwd


**Locating the flags**

Tried locating root.txt first but it didn't work so I tried the command updatedb - which the command locate uses
![Locate1](https://user-images.githubusercontent.com/58709386/82747941-c66af980-9d52-11ea-9f8e-ddbf7a6cf3c2.png)
![Rootflag](https://user-images.githubusercontent.com/58709386/82747940-c66af980-9d52-11ea-89a2-d361491f7939.png)
![Userflag](https://user-images.githubusercontent.com/58709386/82747938-c5d26300-9d52-11ea-9984-cad3c3fa014d.png)

