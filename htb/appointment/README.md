# [HackTheBox] [Appointment]: [Enumeration], [Foothold]

**Target:** [Appointment] | **OS:** Linux | **Difficulty:** Super Easy  
**Attack Vector:** [Enumeration -> Foothold]

---

## 1. Enumeration

```bash
sudo nmap -p- -sS ---min-rate 5000 -T3 10.129.128.97
```

`PORT   STATE SERVICE
80/tcp open  http`

So we have the port 80 open, lets check the web

## 2. Foothold
![Image](https://raw.githubusercontent.com/zyoel/writeups/main/htb/appointment/assets/flag.png)

Really basic SQLi

## 3. Flag
![Image](https://raw.githubusercontent.com/zyoel/writeups/main/htb/appointment/assets/web.png)

Well, this was easy..

Pwned.
