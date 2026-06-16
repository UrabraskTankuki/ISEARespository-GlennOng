# Lab 1b – Linux Services, SSH, Firewalls, Compression, Permissions & Searching

This guide covers **Lab 1b-1 (Linux Services, SSH, Firewalls & Compression)**, **Lab 1b-2 (Linux File Permissions and Group Access Control)**, and **Lab 1b-3 (File Search, Analysis & Archiving in Linux)**.
---

## 1b-1: Apache, Nmap, UFW, SSH & Compression

### Installing the Apache web server

```bash
sudo apt update
sudo apt install apache2
sudo apt install nmap
sudo apt install openssh-server
```
<img width="807" height="637" alt="image" src="https://github.com/user-attachments/assets/1d9f33d8-7945-41b8-8723-e4ea615a47b5" />

Check your IP and visit your own web page:

```bash
ip a
```

Then open Firefox and go to **http://127.0.0.1**

<img width="1101" height="763" alt="image" src="https://github.com/user-attachments/assets/c0549184-db85-4be7-a4e2-d234663185eb" />

**Explanation:** `127.0.0.1` ("localhost" or "loopback") always points back to the machine you're sitting on — every computer can reach itself at this address regardless of network configuration. This is useful for testing a server before exposing it to the rest of the network.

### Editing your web page

```bash
sudo nano /var/www/html/index.html
```

Change the `<title>` and `<h1>` text to something personal, save (Ctrl+O), exit (Ctrl+X), then refresh the browser.

<img width="1109" height="769" alt="image" src="https://github.com/user-attachments/assets/685f1f65-4d5d-4b1a-aa10-b2e104dcb732" />

**Explanation:** `/var/www/html/index.html` is the default file Apache serves. You need `sudo` because this directory is owned by `root`/`www-data`, not your regular user — a normal `nano` (without sudo) would give a "Permission denied" error, which is the answer to the lab's first question about permissions.

---

### Nmap – Port Scanning

```bash
nmap 127.0.0.1
```
<img width="790" height="246" alt="image" src="https://github.com/user-attachments/assets/cad2a67b-7a8a-41c1-bc4f-7127129329ea" />

Now remove Apache and re-scan:

```bash
sudo apt remove apache2
nmap 127.0.0.1
```
<img width="761" height="281" alt="image" src="https://github.com/user-attachments/assets/9e0d5370-7133-40f3-aaa6-2fa95e3f4982" />

(port 80 has disappeared)

Reinstall it:

```bash
sudo apt install apache2
nmap 127.0.0.1
```
<img width="780" height="247" alt="image" src="https://github.com/user-attachments/assets/a22da547-57a8-40a4-b0f9-50aa21468e3e" />

**Explanation:** `nmap` reports which network **ports** are open and what service is listening on them — exactly what an attacker (or a sysadmin doing an audit) would see from the outside. Removing Apache stops anything listening on port 80, so it disappears from the scan; reinstalling it brings it back. This demonstrates that "open ports" map directly to "running services."

---

### UFW – Uncomplicated Firewall

```bash
sudo ufw status verbose
```
<img width="572" height="49" alt="image" src="https://github.com/user-attachments/assets/d613fb92-7e9e-4f49-abda-c5d9297d3368" />

```bash
sudo ufw enable
sudo ufw status verbose
```
<img width="685" height="152" alt="image" src="https://github.com/user-attachments/assets/8ee3eef4-7cbb-4448-9145-46dcb14512f7" />

Re-run nmap:

```bash
nmap 127.0.0.1
```
<img width="729" height="196" alt="image" src="https://github.com/user-attachments/assets/8e603e16-a0c9-4940-acb4-1ddd3820ba39" />

Now allow web traffic:

```bash
sudo ufw allow 80/tcp
sudo ufw status verbose
nmap 127.0.0.1
```
<img width="673" height="288" alt="image" src="https://github.com/user-attachments/assets/ae340a0f-d592-4b02-a6f6-44d9e2daec68" />
<img width="749" height="196" alt="image" src="https://github.com/user-attachments/assets/d70b3309-20c7-4fd5-ad39-f5c6d608cef1" />

**Explanation:** UFW is a simplified front-end for Linux's `iptables` firewall. By default it **denies all incoming connections** — even though Apache is running and "listening," the firewall stops the traffic from reaching it. `ufw allow 80/tcp` punches a specific hole for HTTP traffic only. This is the difference between a *service running* and a *service being reachable*.

---

### SSH

If you're working solo, SSH into your own machine using `localhost`:

```bash
ssh yourusername@localhost
```
<img width="795" height="459" alt="image" src="https://github.com/user-attachments/assets/141acf3e-0a2b-4ac9-a029-c6c2736fad87" />

After entering your password, you'll get a fresh shell prompt — you're now "remotely" logged into your own machine.

```bash
exit
```
<img width="383" height="64" alt="image" src="https://github.com/user-attachments/assets/74cd1809-e878-466b-b20f-b57fec49b79d" />

**Explanation:** SSH (Secure Shell) gives you a remote command-line session on another machine, authenticated by username/password (or key pair). The "host key fingerprint" warning the first time is SSH's way of asking "do you trust this server's identity?" — it's recorded in `~/.ssh/known_hosts` afterwards.

#### Create a new user and inspect /etc/passwd

```bash
less /etc/passwd
```

<img width="793" height="529" alt="image" src="https://github.com/user-attachments/assets/f539392d-f596-4a76-93d8-34f99f74cf93" />

```bash
sudo adduser testuser
```

```bash
less /etc/passwd
```
<img width="659" height="70" alt="image" src="https://github.com/user-attachments/assets/0d54a16e-9e3e-410f-84fe-11858cf06fc6" />

**Explanation:** `/etc/passwd` is the master list of all user accounts on the system (username, UID, GID, home directory, default shell). Creating a user with `adduser` automatically appends a new line here and creates a home directory under `/home/`.

#### SSH as a different user

```bash
ssh testuser@localhost
```
<img width="763" height="303" alt="image" src="https://github.com/user-attachments/assets/e2d5375a-b9f2-4172-99a6-3daf74056aba" />

```bash
exit
exit
```

**Explanation:** By default, `ssh hostname` connects using *your current username*. To log in as someone else you specify `username@host`. This demonstrates that SSH access is governed by the same user accounts and passwords as a local login — there's no separate "SSH password."

---

### Dealing with Compressed Archives

```bash
wget https://www.gutenberg.org/files/76/76-0.txt
wget https://www.gutenberg.org/files/36/36-0.txt (optional)
wget http://www.gutenberg.org/files/12/12-0.txt (optional)
```
<img width="794" height="323" alt="image" src="https://github.com/user-attachments/assets/5e5eaff2-48a1-45e5-9c62-decb520eef74" />

```bash
mkdir books
mv 76-0.txt 36-0.txt 12-0.txt books/
```

```bash
tar cf books.tar books
bzip2 books.tar
ls -la
```
<img width="661" height="29" alt="image" src="https://github.com/user-attachments/assets/a1fc5c06-53fb-48e9-99ed-93338fe3049a" />

**Explanation:** `wget` downloads files directly from the command line (no browser needed). `tar cf books.tar books` bundles the whole `books` directory into a single **tar archive** (no compression yet — `tar` originally stood for "tape archive"). `bzip2 books.tar` then compresses that archive into `books.tar.bz2`. Compare the size of `books.tar.bz2` against the sum of the three original `.txt` files (use `du -sh books`) to calculate your compression ratio — plain text typically compresses to roughly 30-40% of its original size with bzip2.

To reverse the process:

```bash
bunzip2 books.tar.bz2
tar -xvf books.tar
```
<img width="498" height="68" alt="image" src="https://github.com/user-attachments/assets/95c75935-350b-44a1-8d5f-a384c9dc8cf4" />


**Explanation:** `bunzip2` decompresses `.bz2` back into a plain `.tar`, and `tar -xvf` extracts (`x`), verbosely (`v`), from file (`f`) the archive back into individual files/folders.

---

### Extension Challenges

#### Challenge 1: SSH and create a file

```bash
ssh testuser@localhost
cd ~/Desktop
echo "Hi_testuser" > Hi_testuser.txt
exit
```
<img width="794" height="303" alt="image" src="https://github.com/user-attachments/assets/79d1cc4b-ddcc-41bf-83e7-72a0e0bb70a4" />

#### Challenge 2: GUI apps over SSH

```bash
ssh testuser@localhost
gedit Hi_testuser.txt
```
<img width="700" height="200" alt="image" src="https://github.com/user-attachments/assets/dbb08641-91a4-4dac-9382-b9e6da71aa08" />

**Explanation:** A plain SSH session has no GUI ("X11") attached, so graphical apps like `gedit` fail. `nano Hi_testuser.txt` works fine instead, because it's a terminal-only program. This is exactly why server administration relies on terminal editors.

#### Challenge 3: scp (secure copy)

```bash
scp Hi_testuser.txt testuser@localhost:/home/testuser/Desktop/Hi_testuser.txt
```
<img width="801" height="91" alt="image" src="https://github.com/user-attachments/assets/b76a568c-7144-4198-ae04-68dfa26159e7" />

To copy a whole directory:

```bash
scp -r books testuser@localhost:/home/testuser/
```

**Explanation:** `scp` works like `cp`, but over SSH — it can copy files between any two machines that have SSH enabled, encrypting the transfer. The `-r` flag makes it recursive, copying an entire folder and its contents.

#### Challenge 4: Top 10 Gutenberg books

```bash
mkdir gutenberg_top10
cd gutenberg_top10
wget https://www.gutenberg.org/cache/epub/1342/pg1342.txt Pride and Prejudice
wget https://www.gutenberg.org/cache/epub/11/pg11.txt Alice's Adventures in Wonderland
wget https://www.gutenberg.org/cache/epub/84/pg84.txt Frankenstein
```

```bash
cd ..
tar -czf gutenberg_top10.tar.gz gutenberg_top10
scp gutenberg_top10.tar.gz testuser@localhost:/home/testuser/Desktop/
```
<img width="793" height="159" alt="image" src="https://github.com/user-attachments/assets/f10dabb1-deaa-44b2-97b5-c2e2fa369b46" />

**Explanation:** `tar -czf` combines archiving (`-c`, create) and gzip compression (`-z`) in one step, producing a `.tar.gz`. This is the most common archive format you'll see for downloading source code or backups on Linux.

---

## 1b-2: Linux File Permissions and Group Access Control

### Create the users

```bash
sudo adduser alice
sudo adduser bob
sudo adduser charlie
```

### Create the shared directory and files

```bash
sudo mkdir /home/shared
cd /home/shared
sudo touch file{1..10}
ls -l
```
<img width="677" height="331" alt="image" src="https://github.com/user-attachments/assets/792b218a-36ea-4acf-8ef4-ea7f41700af8" />

**Explanation:** `file{1..10}` is "brace expansion" — bash automatically expands this into `file1 file2 ... file10`, saving you typing ten separate `touch` commands.

### Create the group and set ownership/permissions

```bash
sudo groupadd projectgroup
sudo usermod -aG projectgroup alice
sudo usermod -aG projectgroup bob
less /etc/group
```
<img width="349" height="23" alt="image" src="https://github.com/user-attachments/assets/f8d08089-fe3b-48b1-bdb8-c4d147605d7c" />

Now set ownership and permissions on the shared folder:

```bash
sudo chown -R alice:projectgroup /home/shared
sudo chmod -R 770 /home/shared
ls -ld /home/shared
```

<img width="799" height="110" alt="image" src="https://github.com/user-attachments/assets/113a636c-08b4-42f4-860c-aeb4597c58b7" />

Now restrict the *files* so the group can read but not write:

```bash
sudo chmod -R 750 /home/shared
ls -ld /home/shared
```
<img width="704" height="48" alt="image" src="https://github.com/user-attachments/assets/3f7a623b-8f18-4ba5-b881-e840c4074409" />

**Explanation:** Permissions are read as three groups of `rwx` — **owner**, **group**, **others**. `750` = `rwx r-x ---`:
* **alice** (owner) → read, write, execute (`rwx`)
* **projectgroup** members, i.e. **bob** → read, execute only (`r-x`) — can't write
* **everyone else**, i.e. **mallory** → no access at all (`---`)

`chown -R user:group` recursively sets the owner and group on a folder and everything inside it. `usermod -aG group user` *adds* (`-a`, append) a user to a supplementary group without removing their existing groups.

### Test as each user

Switch users with `su`:

```bash
su - alice
ls -l /home/shared
touch /home/shared/alice_test.txt
whoami
exit
```
<img width="771" height="530" alt="image" src="https://github.com/user-attachments/assets/be2f7a92-8f00-45ba-9bdc-5e59bc819795" />

```bash
su - bob
cat /home/shared/file1
nano /home/shared/file1
whoami
exit
```
<img width="791" height="631" alt="image" src="https://github.com/user-attachments/assets/c99e3374-2cfb-48d4-9cdb-f04690a6db21" />
<img width="544" height="176" alt="image" src="https://github.com/user-attachments/assets/be84d337-44fc-4930-a101-5c97f922898b" />

```bash
su - mallory
cd /home/shared
cat /home/shared/file1
whoami
exit
```
<img width="585" height="221" alt="image" src="https://github.com/user-attachments/assets/a141c6d3-66e2-4dd2-96f2-3cee6a5b7b30" />

**Explanation:** This sequence proves the permission model in practice:
* **Alice** (owner, `rwx`) → full control
* **Bob** (group, `r-x`) → can read/view but `nano` fails because he lacks write permission
* **Charlie** (others, `---`) → can't even enter (`cd`) the directory or read any file inside it, because she has zero permissions on `/home/shared` itself

`su - username` switches your shell to that user (prompting for their password), and `whoami` confirms which user you currently are.

### Challenge: Mallory in the sudoers group

```bash
sudo usermod -aG sudo charlie
su - charlie
sudo whoami
```
<img width="630" height="203" alt="image" src="https://github.com/user-attachments/assets/cfa6490c-6302-43cd-bdbb-bc1395d7fbd7" />

```bash
cat /home/shared/file1
```
<img width="576" height="43" alt="image" src="https://github.com/user-attachments/assets/ead60881-cf0b-4037-8586-9c6d68c5fbe9" />

```bash
sudo cat /home/shared/file1
```
<img width="617" height="37" alt="image" src="https://github.com/user-attachments/assets/3fb17163-8a47-4a31-b30f-cb1895c89d13" />

**Explanation:** Adding charlie to the `sudo` group lets her run **any** command as root via `sudo`, completely bypassing the file permissions you set earlier — `root` can read/write/execute anything regardless of `rwx` settings. This demonstrates why sudoers group membership is one of the most security-sensitive settings on a Linux system: it's effectively a master key.

### Cleanup

```bash
sudo rm -rf /home/shared
```
<img width="601" height="46" alt="image" src="https://github.com/user-attachments/assets/0f2c9cf0-f481-43b8-9e70-e43fb4490e2d" />

**Explanation:** `-r` (recursive) is required to delete a directory and its contents; `-f` (force) suppresses confirmation prompts. **Be very careful with `rm -rf`** — there is no "recycle bin" on the command line.

---

## 1b-3: File Search, Analysis & Archiving (Gutenberg Archive)

Download `Gutenberg.tar.bz2` from the unit's LMS page

### Extract the archive
```bash
cd ~/Downloads/
ls
```
<img width="722" height="133" alt="image" src="https://github.com/user-attachments/assets/957cbddc-ffa3-496c-99b9-e3f67921861e" />

```bash
bunzip2 1b-3-Gutenberg.tar.bz2
ls
tar -xvf 1b-3-Gutenberg.tar
```
<img width="733" height="19" alt="image" src="https://github.com/user-attachments/assets/1b18a4d4-1bd8-4264-bf19-3ee727ba6594" />
<img width="641" height="41" alt="image" src="https://github.com/user-attachments/assets/7c23bc44-4e9e-46eb-9b2d-65a9c322f43f" />
<img width="694" height="85" alt="image" src="https://github.com/user-attachments/assets/4fd18956-bad6-444f-b468-a37d88e9e366" />

### Searching for filenames

```bash
find . -name "*.txt"
```
<img width="624" height="90" alt="image" src="https://github.com/user-attachments/assets/ee85583f-af1d-4206-aa29-bf9f318c92cb" />

### Searching for text

```bash
grep -r "times" .
```
<img width="765" height="60" alt="image" src="https://github.com/user-attachments/assets/bcf84d19-570d-458a-8d83-a14387824044" />

To get just a count:

```bash
grep -ro "times" . | wc -l
```
<img width="691" height="66" alt="image" src="https://github.com/user-attachments/assets/47d8d0b4-cec4-49ef-a4d7-d65e7054b0b6" />

To see surrounding context:

```bash
cat frankenstein.txt
cat moby.txt
cat twocities.txt
```
<img width="800" height="132" alt="image" src="https://github.com/user-attachments/assets/e96745bf-4308-4dbc-8c91-a99088915dc5" />

**Explanation:** `grep -r` searches recursively through every file in `.` (current directory). `-o` prints only the matched text (one per line, so piping to `wc -l` counts total occurrences). `-C 3` shows 3 lines of context before and after each match.
```bash
cat *.txt | sed -e 's/\s/\n/g' | sort | uniq -c | sort -nr | head -10
```
<img width="792" height="260" alt="image" src="https://github.com/user-attachments/assets/ca0a8821-1534-439f-a530-b8a5bd4df095" />

| Author of the 255258-byte file | Lobo |
| File with 3rd oldest creation date | 1498.txt |
| Word following "Next day there was a surprise for Jack" | Halliday |

> These are the *official* expected answers from the lab sheet — running the commands above against the actual `Gutenberg.tar.bz2` from LMS should reproduce them. If your numbers differ slightly, double check you extracted the exact archive provided (not the smaller 3-book archive from 1b-1).
