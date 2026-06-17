# Lab 3b – Bash Scripting, Backup Automation & Cron
---

## Part 1 – Bash Basics

### Hello World

```bash
nano ~/hello_bash.sh
```

Type:

```bash
#!/bin/bash
# This is a bash comment
echo "Hello Bash World!"
```

Save (Ctrl+O, Enter, Ctrl+X), then make it executable and run it:

```bash
chmod 777 ~/hello_bash.sh
~/hello_bash.sh
```
<img width="568" height="41" alt="image" src="https://github.com/user-attachments/assets/6dc141c6-36d3-48f1-b58d-aca14fdf377e" />

**Explanation:** The first line `#!/bin/bash` is called the **shebang** — it tells the OS to use bash to interpret this file. Without it, the OS doesn't know which language the script is written in. `chmod 777` gives read/write/execute permissions to everyone, making the file runnable.

---

### Variables

```bash
nano ~/hello_bash.sh
```

Replace the echo line with:

```bash
#!/bin/bash
a="Hello bash using variables"
echo $a
```

```bash
~/hello_bash.sh
```
<img width="570" height="45" alt="image" src="https://github.com/user-attachments/assets/32a19b88-c3f6-4b67-8d23-d50822c30e96" />

**Explanation:** Variables in bash are created with `name=value` (no spaces around `=`) and read back with `$name`. Unlike many languages, bash variables have no type — they're all treated as strings unless you use arithmetic syntax.

---

### Basic calculations

```bash
nano ~/hello_bash.sh
```

```bash
#!/bin/bash
a=10
b=5

c=$((a+b))
echo "Addition: $c"

d=$((a-b))
echo "Subtraction: $d"

e=$((a*b))
echo "Multiplication: $e"

f=$((a/b))
echo "Division: $f"
```

```bash
~/hello_bash.sh
```
<img width="568" height="104" alt="image" src="https://github.com/user-attachments/assets/b28c22e4-aaab-448b-a4c8-009572b10abb" />

**Explanation:** `$(( ))` is bash's arithmetic expansion — it evaluates the expression inside as integer maths and returns the result. Note that bash only does **integer** arithmetic: `$((7/2))` gives `3`, not `3.5`. For decimal maths you'd need `bc` or `python3`.

---

### Modify the Code 1 – For loop sum
```bash
#!/bin/bash
for ((i=0;i<10;i++));
do
  echo $i
done
```

This prints 0 through 9. Now modify it to **sum** each number:

```bash
nano ~/loop_sum.sh
```

```bash
#!/bin/bash
total=0

for ((i=0;i<10;i++));
do
  total=$((total + i))
  echo "i=$i  running total=$total"
done

echo "Final sum: $total"
```

```bash
chmod 777 ~/loop_sum.sh
~/loop_sum.sh
```
<img width="545" height="260" alt="image" src="https://github.com/user-attachments/assets/b954a3f4-7937-4c2a-8f4d-b6628391447e" />

**Explanation:** `total=$((total + i))` updates the running total each iteration. The sum of 0+1+2+...+9 = 45. This pattern — initialise a variable before the loop, update it inside the loop, use it after — is fundamental to almost all scripting.

---

## Part 2 – Creating the Backup Script

### Step 1 – Create the files and folders to back up

```bash
mkdir -p ~/Documents
cd ~/Documents
touch file1 file2 file3 file4 file5
mkdir testfolder
cd testfolder
touch file11 file22 file33 file44 file55
cd ~
```

Verify the structure:

```bash
find ~/Documents -print
```
<img width="591" height="324" alt="image" src="https://github.com/user-attachments/assets/ae39fdd3-3497-418b-bfff-fea5cb476a44" />

---

### Step 2 – Create the basic backup script

Create the backup destination folder:

```bash
mkdir -p ~/backup
```

Now create the script:

```bash
nano ~/testscript
```

Type:

```bash
#!/bin/bash
cp -R /home/$USER/Documents/* /home/$USER/backup/
echo "Backup complete."
```

Save and exit, then make it executable and test it:

```bash
chmod 777 ~/testscript
~/testscript
```
<img width="422" height="40" alt="image" src="https://github.com/user-attachments/assets/eb3105c2-b474-4df2-bf9d-9753c161cbdd" />

Verify the files were copied:

```bash
find ~/backup -print
```
<img width="517" height="328" alt="image" src="https://github.com/user-attachments/assets/f154d16f-6d65-47ca-96ef-b172242de6e4" />

**Explanation:** `cp -R` copies recursively — it copies the directory and everything inside it, preserving the folder structure. The `*` wildcard means "everything inside `Documents/`". You copy *into* `backup/` rather than copying `Documents/` into itself, which would fail.

---

### Step 3 – Make the script available system-wide

Move the script to `/usr/bin/` so any user can run it from anywhere:

```bash
sudo mv ~/testscript /usr/bin/testscript
sudo chown $USER /usr/bin/testscript
```
<img width="737" height="65" alt="image" src="https://github.com/user-attachments/assets/33fcfa7d-5135-4bb5-9877-2ad05716ca52" />

Test it runs from anywhere — first move to a completely different directory:

```bash
cd /tmp
testscript
```
<img width="452" height="62" alt="image" src="https://github.com/user-attachments/assets/62a0b12f-b9ea-4d9b-974b-6252a04670ff" />

Check what directories Linux automatically searches for commands:

```bash
echo $PATH
```
<img width="791" height="58" alt="image" src="https://github.com/user-attachments/assets/f9b5ab77-21e9-4dbf-a745-198752a63f57" />

**Explanation:** `$PATH` is a list of directories Linux checks (in order) whenever you type a command. Because `/usr/bin` is in `$PATH`, typing `testscript` finds and runs `/usr/bin/testscript` automatically — the same reason typing `ls` works without typing `/bin/ls`. `chown $USER` transfers ownership from root (who ran `sudo mv`) back to your user so you can edit the script later.

---

### Step 4 – Add zip compression with a date-based filename

Update the script to zip the backup with today's date as the filename:

```bash
sudo nano /usr/bin/testscript
```

Replace the contents with:

```bash
#!/bin/bash

# Set date variable in dd_mm_yy format
now=$(date +"%d_%m_%y")

# Recursively copy Documents into backup folder
cp -R /home/$USER/Documents/* /home/$USER/backup/

# Zip the backup folder and name it with today's date
zip -r /home/$USER/$now.zip /home/$USER/backup/*

echo "Backup complete: $now.zip"
```

Test it:

```bash
testscript
```
<img width="661" height="347" alt="image" src="https://github.com/user-attachments/assets/a946a6c4-8d71-44f3-8cf4-86cf6e88f484" />

Confirm the zip file was created:

```bash
ls -lh ~/*.zip
```
<img width="744" height="40" alt="image" src="https://github.com/user-attachments/assets/57034a99-3f7f-453d-8615-020db749a994" />

**Explanation:** `$(date +"%d_%m_%y")` runs the `date` command and inserts its output into the variable `now` — this is called **command substitution**. The format `%d_%m_%y` gives you `15_06_26` for 15 June 2026. Each day's backup gets a unique filename so old backups aren't overwritten.

---

### Step 5 – Schedule the script with Cron

Cron is Linux's task scheduler. Edit the system crontab:

```bash
sudo nano /etc/crontab
```

Scroll to the bottom and add this line:

```
9 * * * * yourname /usr/bin/testscript
```
<img width="800" height="145" alt="image" src="https://github.com/user-attachments/assets/01f076f4-3650-4f99-9368-8145e4c6d15d" />

Save (Ctrl+O, Enter, Ctrl+X).

Verify the entry was saved:

```bash
cat /etc/crontab | grep testscript
```
<img width="724" height="42" alt="image" src="https://github.com/user-attachments/assets/9f1ca9f1-d9ed-45fa-95e3-d5462b59c1d1" />

**Explanation:** The five fields before the username and command mean:

| Field | Value | Meaning |
|---|---|---|
| Minute | 9 | At minute 9 |
| Hour | * | Every hour |
| Day of month | * | Every day |
| Month | * | Every month |
| Day of week | * | Every day of week |

So `9 * * * *` = "at 9 minutes past every hour, every day." Your script will now run automatically — e.g. 10:09, 11:09, 12:09, etc. — without any human intervention.

To confirm cron is running:

```bash
sudo systemctl status cron
```
<img width="798" height="484" alt="image" src="https://github.com/user-attachments/assets/a1324f12-e70f-4419-b924-4b149189cdd5" />

---

### Step 6 – Power Management Detour

This section runs on your **local Lubuntu VM only** (not EC2 — cloud servers should never be suspended).
**Suspend** (sleep to RAM — fast resume, still uses a little power):

```bash
systemctl suspend
```
<img width="1077" height="800" alt="image" src="https://github.com/user-attachments/assets/fb318be3-90f5-4330-95e6-88c44f230924" />

**Hibernate** (sleep to disk — no power used, slower resume):

```bash
systemctl hibernate
```

To schedule a suspend at 6:00pm using cron, you would add to crontab:

```
0 18 * * * root systemctl suspend
```

**Explanation:** `0 18 * * *` = "at 18:00 (6:00pm) every day." This is how organisations can save power on lab computers overnight without needing someone to manually shut them down — the same principle as a smart plug timer, but more precise and scriptable.

---

### Step 7 – Export the backup to your EC2 cloud server

Now that the script creates a dated zip file, extend it to also push the backup to your cloud server via `scp`.

First, from your local VM, do a **one-time manual SSH as root** to accept your EC2 server's host key (required so cron can SSH non-interactively later):

```bash
sudo ssh -i /home/$USER/Downloads/webserver-key.pem ubuntu@glennisea.duckdns.org
```

Type `yes` at the fingerprint prompt, then `exit`.

Now update the script:

```bash
sudo nano /usr/bin/testscript
```

```bash
#!/bin/bash

# Variables
now=$(date +"%d_%m_%y")
BACKUP_DIR=/home/$USER/backup
DOCS_DIR=/home/$USER/Documents
PEM_KEY=/home/$USER/Downloads/webserver-key.pem
CLOUD_SERVER=ubuntu@glennisea.duckdns.org

# Step 1: Copy Documents into backup folder
cp -R $DOCS_DIR/* $BACKUP_DIR/

# Step 2: Zip the backup with today's date as filename
zip -r /home/$USER/$now.zip $BACKUP_DIR/*

# Step 3: Export the zip to the cloud server
scp -i $PEM_KEY /home/$USER/$now.zip $CLOUD_SERVER:/home/ubuntu/

echo "Backup complete and exported: $now.zip"
```

Test it manually first:

```bash
testscript
```
<img width="770" height="345" alt="image" src="https://github.com/user-attachments/assets/7d6ad705-f7ae-442d-a5a0-8053791b86c4" />

Verify the file arrived on EC2:

```bash
ssh -i ~/Downloads/webserver-key.pem ubuntu@glennisea.duckdns.org "ls -lh ~/*.zip"
```
<img width="787" height="64" alt="image" src="https://github.com/user-attachments/assets/a394bc21-f492-46a2-b23b-4ab44dd54362" />

**Explanation:** The script now has three stages — copy, zip, export. By providing complete absolute paths (e.g. `/home/yourname/backup` rather than `~/backup`) the script works correctly when run by cron, which doesn't have your normal user environment and doesn't know what `~` means.

---

## Challenge 1 – Run the script at boot time

To run your backup script every time the machine boots, add it to cron using the special `@reboot` keyword:

```bash
sudo nano /etc/crontab
```

Add this line:

```
@reboot yourname /usr/bin/testscript
```

Alternatively, use your personal crontab (no sudo needed):

```bash
crontab -e
```

Add:

```
@reboot /usr/bin/testscript
```

<img width="401" height="45" alt="image" src="https://github.com/user-attachments/assets/94e0d47d-3199-4b3c-9a8a-ca35c74180ac" />

```bash
testscript
ls ~/*.zip
```

**Explanation:** `@reboot` is a special cron shorthand meaning "run once at system startup." This is how servers automatically start services, run health checks, or take a snapshot on boot — without any human needing to log in first.

---

## Challenge 2 – Custom Login Message (MOTD) with neofetch and figlet

### Install the tools

```bash
sudo apt install neofetch figlet
```

If it does not work use this instead:
```bash
sudo apt install curl
curl -o neofetch https://raw.githubusercontent.com/dylanaraps/neofetch/master/neofetch
chmod +x neofetch
sudo mv neofetch /usr/local/bin/neofetch
```

### Test them

```bash
neofetch
```
<img width="800" height="499" alt="image" src="https://github.com/user-attachments/assets/ca8bca7b-22b7-4ed3-9994-711f54164bb9" />

```bash
figlet "Hello Glenn"
```
<img width="556" height="143" alt="image" src="https://github.com/user-attachments/assets/2401cb0a-5f21-40a4-85e9-e20e2c59de44" />

### Add them to the Message of the Day (MOTD)

The MOTD is displayed every time a user logs in via SSH or opens a terminal. Create a custom MOTD script:

```bash
sudo nano /etc/update-motd.d/99-custom
```

```bash
#!/bin/bash
figlet "Welcome to My Server"
echo ""
neofetch
echo ""
echo "Backup script runs every hour at minute 9."
echo "Last backup: $(ls -t ~/backup/*.zip 2>/dev/null | head -1)"
```

```bash
sudo chmod +x /etc/update-motd.d/99-custom
```

Test it immediately:

```bash
run-parts /etc/update-motd.d/
```
<img width="797" height="518" alt="image" src="https://github.com/user-attachments/assets/e30de608-df17-4498-87ee-f2246ba51c47" />

Now when you SSH into this machine:

```bash
ssh yourname@localhost
```
<img width="797" height="525" alt="image" src="https://github.com/user-attachments/assets/1e28a817-2173-46c4-ad67-9e573edb4154" />

**Explanation:** `/etc/update-motd.d/` is a directory of scripts that Ubuntu runs in order (by filename number) to build the MOTD. By creating `99-custom` (high number = runs last), your message appears at the bottom of the login banner. This is how sysadmins display server info, warnings, or notices to all users who log in.
