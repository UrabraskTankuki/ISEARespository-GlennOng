# Lab 2b – Amazon EC2, Bash Scripting, AWK/Regex & Apache Log Parsing
---

## 2b-1: Amazon EC2 Web Services

### Step 1 – Create an AWS account and launch an instance

1. Go to https://aws.amazon.com/ec2/ and sign up (use your Murdoch email, select "Business/School" to minimise credit card delays) <img width="1835" height="814" alt="image" src="https://github.com/user-attachments/assets/12dd5058-fb70-4aab-a4f0-b6d0dfd140ad" />

2. In the EC2 console, click **Launch Instance** <img width="482" height="320" alt="image" src="https://github.com/user-attachments/assets/c1ae0345-b9fc-45e2-ba6c-d4650b98a65b" />

3. Settings to use:
   - **Name:**  e.g. `my-webserver`
   - **AMI:** Ubuntu Server 22.04 LTS (Free tier eligible)<img width="1163" height="620" alt="image" src="https://github.com/user-attachments/assets/871bdac2-ae39-4a3a-9498-9e9e1815cdeb" />

   - **Instance type:** t2.micro (Free tier eligible)<img width="1153" height="322" alt="image" src="https://github.com/user-attachments/assets/8ea4250e-c429-4baf-939d-7468660a4435" />

   - **Key pair:** Click "Create new key pair", name it `webserver-key`, download the `.pem` file — **keep this safe, you cannot re-download it**   <img width="1164" height="253" alt="image" src="https://github.com/user-attachments/assets/da16e2f2-0c56-455c-9752-ff5a47dd1fa6" />

   - **Security group:** Create a new one called `ssh-and-web`, with rules:
     - SSH (port 22) — allows you to log in
     - HTTP (port 80) — allows web traffic <img width="1158" height="631" alt="image" src="https://github.com/user-attachments/assets/78b86eea-6d89-4cd2-a7e4-e8e4d1c1d60c" />

4. Click **Launch Instance** and wait ~1 minute for it to start
5. Note your instance's **Public IP address** from the EC2 dashboard
<img width="169" height="66" alt="image" src="https://github.com/user-attachments/assets/f60adcca-ee67-4312-b352-e720274c2fd6" />

---

### Step 2 – Note your public IP

In the EC2 dashboard, click your instance and find:
```
Public IPv4 address: 13.236.209.73
```
<img width="885" height="288" alt="image" src="https://github.com/user-attachments/assets/c8387bc5-7db5-47b6-b5b5-98b3c0d3f437" />

---

### Step 3 – SSH into your EC2 instance

Open a terminal and navigate to the `.pem` file:

```bash
cd ~/Downloads
ls webserver-key.pem
```
<img width="612" height="100" alt="image" src="https://github.com/user-attachments/assets/c3cca628-9c56-4dd2-a502-c0548057570d" />

Set correct permissions on the key file (required — SSH refuses keys that are too open):

```bash
chmod 400 webserver-key.pem
```
<img width="703" height="22" alt="image" src="https://github.com/user-attachments/assets/8ffef50e-cbe7-4d45-b22f-3c5210c29012" />

SSH into your EC2 instance:

```bash
ssh -i "webserver-key.pem" ubuntu@13.236.208.73
```

You are now inside your cloud server. Notice the prompt changed — everything you type now runs on AWS, not your local VM.

**Explanation:** The `.pem` file is a private key that proves your identity to AWS — there's no password. `chmod 400` makes the file readable only by you; SSH refuses to use a key file that other users on the system could also read, as a security measure.

---

### Step 4 – Install Apache

```bash
sudo apt update
sudo apt install apache2 -y
```
<img width="780" height="112" alt="image" src="https://github.com/user-attachments/assets/ec3fa0ef-d5a5-47b4-88f1-d60320fdd740" />

Now open Firefox on your **local Lubuntu VM** and visit:
```
http://13.236.208.73
```

**Explanation:** A web server on a machine in an AWS data centre (anywhere in the world), and reach it from your laptop over the public internet — that's the core value proposition of cloud computing.

---

### Step 5 – Edit index.html

```bash
sudo nano /var/www/html/index.html
```

Delete the existing content and replace it with something simple, e.g.:

```
rename title to whatever you want
```

Save (Ctrl+O, Enter) and exit (Ctrl+X). Refresh your browser.

<img width="1088" height="710" alt="image" src="https://github.com/user-attachments/assets/7138358c-204a-4104-aed7-a00243236cd2" />

---

### Step 6 – Link files from your webserver

Download a file directly to your EC2 server:

```bash
wget http://www.eecs.berkeley.edu/Pubs/TechRpts/2009/EECS-2009-28.pdf
```
<img width="796" height="93" alt="image" src="https://github.com/user-attachments/assets/2555fc4a-67ca-4d24-bbcb-7a42ace1f1fd" />

Move it into the web directory:

```bash
sudo cp EECS-2009-28.pdf /var/www/html/
```

Test it in your browser:
```
http://13.236.208.73/EECS-2009-28.pdf
```
<img width="1065" height="652" alt="image" src="https://github.com/user-attachments/assets/fff01118-ab0b-4c53-873e-a3fe410debce" />

```bash
sudo nano /var/www/html/index.html
```

Add this line inside the `<body>` tag:

```html
<a href="EECS-2009-28.pdf">Download the weekly reading (PDF)</a>
```
<img width="274" height="30" alt="image" src="https://github.com/user-attachments/assets/ff6dd81f-7f3d-4742-897e-8c3919118f16" />

Refresh your browser and click the link to confirm it works.

**Explanation:** `wget` downloads files from the internet directly to the server. This is how you'd deploy files to a web server in practice: download or `scp` them to the server, move them into `/var/www/html/`, and link them from your HTML.

---

### Step 7 – Budgets and cost alert

**Important** — AWS charges by the hour. Even t3.micro has limits on free tier hours. Set a billing alert now:

1. In the AWS console, click your **account name (top right) → Billing Dashboard**
2. Click **Billing and Cost Management** 
3. **Budget Setup**: Use a template <img width="1407" height="307" alt="image" src="https://github.com/user-attachments/assets/21498cd9-8c25-48a0-9eac-4dc3077ae45a" />

4.  **Templates**: Zero spend Budget <img width="1403" height="376" alt="image" src="https://github.com/user-attachments/assets/4cf34312-43e4-46fe-8411-74f172b5a445" />

5. Check the details of the template then **create budget** <img width="1392" height="644" alt="image" src="https://github.com/user-attachments/assets/77275b08-1358-49d6-a363-7738242b5001" />

---

### Challenges

#### Challenge 1 – Ping servers around the world and note latency

```bash
ping -c 4 google.com
ping -c 4 8.8.8.8
ping -c 4 yahoo.co.jp
ping -c 4 bbc.co.uk
```
<img width="787" height="177" alt="image" src="https://github.com/user-attachments/assets/f8400cc3-221a-4189-bf3c-28c05a7e39de" />
<img width="589" height="101" alt="image" src="https://github.com/user-attachments/assets/589c4443-becf-46ff-8358-fbc338904fda" />
<img width="645" height="104" alt="image" src="https://github.com/user-attachments/assets/012cc4a8-2b5e-4305-be5c-87f2271012bd" />
<img width="637" height="133" alt="image" src="https://github.com/user-attachments/assets/55c1e53d-5e9d-47c1-b776-077140b0713f" />

Note how latency increases with physical distance — low ms to nearby servers, hundreds of ms to the other side of the world.

**Alternatives to Amazon EC2:** Microsoft Azure, Google Cloud Platform (GCP), DigitalOcean, Linode (Akamai), Oracle Cloud, Vultr.

#### Challenge 2 – Upload a local file via scp

On your **local Lubuntu VM** (not inside SSH):

```bash
scp -i ~/Downloads/webserver-key.pem ~/Downloads/frankenstein.txt ubuntu@13.236.208.73:/home/ubuntu/
```
<img width="796" height="60" alt="image" src="https://github.com/user-attachments/assets/fb727b6b-1ac6-48a6-8ab8-49ec595c78db" />

Then back on your EC2 server:

```bash
sudo cp /home/ubuntu/frankenstein.txt /var/www/html/
```

Visit `http://203.0.113.45/frankenstein.txt` in your browser to confirm.
<img width="752" height="162" alt="image" src="https://github.com/user-attachments/assets/85f72c44-6ba5-4c75-a7fa-4c02afc20e4a" />

#### Challenge 3 – Custom HTML page

```bash
sudo nano /var/www/html/index.html
```

Create a basic but complete HTML page:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>My Server - Your Name</title>
    <style>
        body { font-family: Arial, sans-serif; max-width: 800px; margin: 50px auto; }
        h1 { color: #333; }
    </style>
</head>
<body>
    <h1>Welcome to My EC2 Web Server</h1>
    <p>Hosted on Amazon EC2 — Ubuntu 22.04 LTS</p>
    <h2>Files available:</h2>
    <ul>
        <li><a href="EECS-2009-28.pdf">Weekly Reading (PDF)</a></li>
        <li><a href="frankenstein.txt">Frankenstein (text)</a></li>
    </ul>
</body>
</html>
```
<img width="947" height="375" alt="image" src="https://github.com/user-attachments/assets/fe26043d-145f-4898-8d34-57911b610900" />

**Explanation:** `<!DOCTYPE html>` tells the browser this is HTML5. The `<style>` block is inline CSS — basic but enough to make the page look cleaner. `<a href="...">` creates clickable hyperlinks to your uploaded files.

---

## 2b-2a: Bash Scripting

All commands in this section run on your **local Lubuntu VM** (you can exit your EC2 SSH session for now).

### Part 1 – File system navigation and management

```bash
mkdir ~/LabFiles
cd ~/LabFiles
pwd
```
<img width="603" height="85" alt="image" src="https://github.com/user-attachments/assets/4854c30d-e283-491e-a7a0-95675337c6cb" />

```bash
touch notes.txt
echo "This is a Bash scripting lab." > notes.txt
cat notes.txt
```
<img width="786" height="104" alt="image" src="https://github.com/user-attachments/assets/2891d9ca-efc6-4276-9850-7234a6ae886f" />

```bash
cp notes.txt backup_notes.txt
mv backup_notes.txt old_notes.txt
ls
```
<img width="743" height="85" alt="image" src="https://github.com/user-attachments/assets/e5fe7520-58aa-4d8a-b286-f8c185eb1d9f" />

```bash
rm old_notes.txt
ls
```
<img width="614" height="103" alt="image" src="https://github.com/user-attachments/assets/ef7f125c-dea9-4bec-aee6-623a0d39770f" />

**Reflection answers:**
- `mkdir` creates a new directory
- `cat` views file contents without opening a GUI editor
- `cp` duplicates a file (original stays); `mv` moves or renames it (original disappears)

---

### Part 2 – Hello World script

```bash
cd ~/LabFiles
nano hello_world.sh
```

Type exactly:

```bash
#!/bin/bash
echo "Hello, World!"
```
<img width="521" height="74" alt="image" src="https://github.com/user-attachments/assets/e60dbaff-60c9-45e7-acba-5551574bb98a" />

Save (Ctrl+O, Enter, Ctrl+X), then:

```bash
chmod 777 hello_world.sh
./hello_world.sh
```
<img width="681" height="61" alt="image" src="https://github.com/user-attachments/assets/a6f3c2ae-c8f6-4662-aaed-c41e1968bfd0" />

Now modify it:

```bash
nano hello_world.sh
```

Change the echo line to:

```bash
echo "Welcome to the Bash scripting lab!"
```

```bash
./hello_world.sh
```
<img width="628" height="65" alt="image" src="https://github.com/user-attachments/assets/2a1b3f4c-06e3-49a2-acd1-4ef8b80481c9" />

**Reflection answers:**
- `chmod 777` (or `chmod +x`) makes the file executable — without this, trying to run `./hello_world.sh` gives "Permission denied"
- `#!/bin/bash` is the **shebang** — it tells the OS which interpreter to use for this script (bash, not python or sh). Without it, the OS might not know how to run the file
- To personalise: change `echo "Hello, World!"` to `echo "Hello, $USER!"` — `$USER` is a variable that automatically holds your username

---

### Part 3 – Loops and conditionals

```bash
nano ~/LabFiles/system_info.sh
```

Type:

```bash
#!/bin/bash

echo "System Information Script"

# Display current user
echo "Current User: $(whoami)"

# Loop through the next five numbers
for i in {1..5}
do
    echo "Iteration: $i"
    sleep 1s
done

# Conditional Statement
read -p "Enter a number between 1 and 10: " number

if ! [[ "$number" =~ ^[0-9]+$ ]]; then
    echo "Invalid input: Please enter a numeric value."
    exit 1
fi

if [ "$number" -le 5 ]; then
    echo "You entered a number less than or equal to 5."
elif [ "$number" -le 10 ]; then
    echo "You entered a number greater than 5 but less than or equal to 10."
else
    echo "Number out of range."
fi
```

```bash
chmod 777 ~/LabFiles/system_info.sh
~/LabFiles/system_info.sh
```

**Expected output:**
```
System Information Script
Current User: yourname
Iteration: 1
Iteration: 2
Iteration: 3
Iteration: 4
Iteration: 5
Enter a number between 1 and 10: 7
You entered a number greater than 5 but less than or equal to 10.
```
<img width="720" height="219" alt="image" src="https://github.com/user-attachments/assets/fd8e801c-744c-47c3-927a-718a1743e0ca" />

**Reflection:**
- The `for` loop iterates over the sequence `{1..5}` — bash expands this to 1 2 3 4 5, running the loop body once per value with a 1-second pause between
- A number greater than 10 triggers the `else` branch: "Number out of range."
- Invalid input (e.g. "abc") is caught by the regex check `^[0-9]+$` — this matches only strings made entirely of digits, so letters cause the "Invalid input" message and exit

---

### Part 4 – System resource monitoring script

```bash
nano ~/LabFiles/resource_monitor.sh
```

Type:

```bash
#!/bin/bash

echo "System Resource Monitoring"

# Number of iterations
read -p "How many times would you like to monitor the system? " iterations

# Loop based on user input
for ((i=1; i<=iterations; i++))
do
    echo "----- Monitoring $i -----"

    # Display CPU usage
    echo "CPU Usage:"
    top -b -n1 | grep "Cpu(s)"

    # Display Memory usage
    echo "Memory Usage:"
    free -h

    # Display Disk usage
    echo "Disk Usage:"
    df -h | grep "^/dev"

    echo "-----------------------"
    sleep 5s
done

echo "Monitoring complete."
```

```bash
chmod 777 ~/LabFiles/resource_monitor.sh
~/LabFiles/resource_monitor.sh
```
<img width="802" height="336" alt="image" src="https://github.com/user-attachments/assets/10f0b1e5-5cf0-4416-97c8-a8e79925695f" />
<img width="801" height="310" alt="image" src="https://github.com/user-attachments/assets/4ac62edc-a916-4e67-a4b0-7de2214d9a9d" />

Enter `2` when prompted.


**Reflection:**
- `free -h` shows total/used/free RAM and swap space in human-readable units. "Swap" is disk space used as overflow when RAM fills up — high swap usage means you need more RAM
- To monitor network usage, add: `ifstat 1 1` or `cat /proc/net/dev` inside the loop
- Automating monitoring means you can catch problems (disk filling up, CPU spikes) before they cause failures — critical on servers that run 24/7 without anyone watching

---

## 2b-2b: AWK and Regex Reference

These are reference tools. Run each command to practise — no scripts to write, just commands to execute.

### Set up sample data

```bash
mkdir ~/LabFiles/awk_practice
cd ~/LabFiles/awk_practice

cat > data.txt << 'EOF'
Alice 90 85
Bob 88 78
Carol 82 95
Dave 70 60
EOF
```
<img width="779" height="172" alt="image" src="https://github.com/user-attachments/assets/3af697dc-d9a3-425a-bf7e-1aeb4099a275" />

```bash
cat data.txt
```
<img width="671" height="111" alt="image" src="https://github.com/user-attachments/assets/71096930-7ffe-45b9-9034-b217b4b9590e" />

### Basic AWK commands

```bash
awk '{ print }' data.txt
```
<img width="801" height="108" alt="image" src="https://github.com/user-attachments/assets/700a02a4-5ed8-4177-955b-c7ca854a4511" />

```bash
awk '{ print $1 }' data.txt
```
wak<img width="797" height="133" alt="image" src="https://github.com/user-attachments/assets/a1b567b3-3418-4b73-b078-cb0ee4d472da" />

```bash
awk '{ print $2, $3 }' data.txt
```
<img width="795" height="129" alt="image" src="https://github.com/user-attachments/assets/06ac0771-2e10-45fb-97e7-276b46197f23" />

```bash
awk '{ print $1, ($2 + $3) / 2 }' data.txt
```
<img width="796" height="130" alt="image" src="https://github.com/user-attachments/assets/d0684fda-5270-426c-b16e-42aeb57eed14" />

```bash
awk '{ print NR, $0 }' data.txt
```
<img width="797" height="126" alt="image" src="https://github.com/user-attachments/assets/e99ed18a-ed06-4f09-a4ff-7ca09c26c060" />

```bash
awk '{ sum += $2 } END { print "Average score 1:", sum/NR }' data.txt
```
<img width="793" height="66" alt="image" src="https://github.com/user-attachments/assets/e94cfc19-c305-4f16-ab42-cf7ad8fe5e16" />

```bash
awk '$2 > 85 { print $1, "passed" }' data.txt
```
<img width="787" height="89" alt="image" src="https://github.com/user-attachments/assets/fefd73f6-f3fa-4640-a1ec-3fa065956b5a" />

```bash
awk 'BEGIN { print "Name  Avg" } { print $1, ($2+$3)/2 } END { print "Done" }' data.txt
```
<img width="798" height="168" alt="image" src="https://github.com/user-attachments/assets/f40e4c6e-8d8c-460e-ba92-acde343c37d0" />

**Explanation:** AWK processes text **line by line**, splitting each line into fields (`$1`, `$2`, etc.) separated by whitespace by default. `BEGIN{}` runs once before any input; `END{}` runs once after all input. This makes AWK ideal for processing logs, CSVs, and structured text output without writing a full Python/Perl script.

### AWK with /etc/passwd

```bash
awk -F: '{ print $1, $6 }' /etc/passwd | head -10
```

**Explanation:** `-F:` sets the field separator to `:` instead of whitespace — since `/etc/passwd` uses colons, `$1` = username and `$6` = home directory.

### Inline test
<img width="802" height="266" alt="image" src="https://github.com/user-attachments/assets/9fc016e9-40a5-407c-bf81-4e3f99bdab5f" />

```bash
echo -e "a b c\nd e f" | awk '{ print $2 }'
```
<img width="795" height="82" alt="image" src="https://github.com/user-attachments/assets/6d4e834f-c4be-4576-a7f0-392f1ea7a28d" />

---

## 2b-2c: Apache Log Parsing

### Step 1 – Generate some Apache log data

Make sure Apache is running on your local VM and visit a few pages to generate log entries:

```bash
sudo systemctl start apache2
curl http://localhost/
curl http://localhost/doesnotexist
curl http://localhost/index.html
```

Check that logs were generated:

```bash
sudo tail -5 /var/log/apache2/access.log
```
<img width="794" height="305" alt="image" src="https://github.com/user-attachments/assets/58dfc50e-6b9a-481a-ac40-b446e0035494" />

---

### Script 1 – CSV Output Parser

```bash
nano ~/LabFiles/apache_csv.sh
```

```bash
#!/bin/bash

LOGFILE="/var/log/apache2/access.log"
REGEX='^([0-9]+\.[0-9]+\.[0-9]+\.[0-9]+) .* \[([^\]]+)\] "([A-Z]+) ([^ ]+) HTTP/[0-9.]+" ([0-9]{3}) ([0-9]+|-)'

echo "ip,date_time,method,url,status,bytes"

while IFS= read -r line; do
    if [[ $line =~ $REGEX ]]; then
        ip="${BASH_REMATCH[1]}"
        datetime="${BASH_REMATCH[2]}"
        method="${BASH_REMATCH[3]}"
        url="${BASH_REMATCH[4]}"
        status="${BASH_REMATCH[5]}"
        bytes="${BASH_REMATCH[6]}"

        echo "$ip,\"$datetime\",$method,$url,$status,$bytes"
    fi
done < "$LOGFILE"
```

```bash
chmod 777 ~/LabFiles/apache_csv.sh
sudo ~/LabFiles/apache_csv.sh
```
<img width="766" height="63" alt="image" src="https://github.com/user-attachments/assets/f3ea10b9-df55-4381-9b75-07cd6f9124e7" />

**Explanation:** This script reads Apache's raw log format and restructures each line into a clean CSV. `BASH_REMATCH` is an array that holds what each group `(...)` in the regex captured. The regex itself breaks down like this:
- `([0-9]+\.[0-9]+\.[0-9]+\.[0-9]+)` → captures the IP address
- `\[([^\]]+)\]` → captures the timestamp inside `[...]`
- `"([A-Z]+) ([^ ]+) HTTP/[0-9.]+"` → captures the HTTP method (GET/POST) and URL
- `([0-9]{3})` → captures the 3-digit status code
- `([0-9]+|-)` → captures bytes sent (or `-` if unknown)

---

### Script 2 – Failed Requests Only

```bash
nano ~/LabFiles/apache_failed.sh
```

```bash
#!/bin/bash

LOGFILE="/var/log/apache2/access.log"
REGEX='^([0-9]+\.[0-9]+\.[0-9]+\.[0-9]+) .* \[([^\]]+)\] "([A-Z]+) ([^ ]+) HTTP/[0-9.]+" ([0-9]{3}) ([0-9]+|-)'

echo "FAILED REQUESTS (status >= 400):"
echo "IP Address     | Date/Time              | Method | URL           | Status | Bytes"
echo "--------------------------------------------------------------------------------"

while IFS= read -r line; do
    if [[ $line =~ $REGEX ]]; then
        status="${BASH_REMATCH[5]}"
        if (( status >= 400 )); then
            ip="${BASH_REMATCH[1]}"
            datetime="${BASH_REMATCH[2]}"
            method="${BASH_REMATCH[3]}"
            url="${BASH_REMATCH[4]}"
            bytes="${BASH_REMATCH[6]}"

            printf "%-14s | %-21s | %-6s | %-13s | %-6s | %-6s\n" \
                "$ip" "$datetime" "$method" "$url" "$status" "$bytes"
        fi
    fi
done < "$LOGFILE"
```

```bash
chmod 777 ~/LabFiles/apache_failed.sh
sudo ~/LabFiles/apache_failed.sh
```
<img width="796" height="154" alt="image" src="https://github.com/user-attachments/assets/b2455f4c-d361-44ca-9137-5e87989776cb" />

**Explanation:** HTTP status codes 400 and above indicate errors — 4xx means the client did something wrong (bad URL, unauthorised, etc.), 5xx means the server had an error. Filtering to only these lines is a common first step in security auditing and debugging — you're looking for attackers probing for vulnerabilities, or broken links on your site.

---

### Quick AWK one-liners on Apache logs

```bash
sudo awk '{ print $1 }' /var/log/apache2/access.log | sort | uniq -c | sort -nr | head -10
```

**Expected output:** Top 10 IP addresses by number of requests, most frequent first.

```bash
sudo awk '{ print $9 }' /var/log/apache2/access.log | sort | uniq -c | sort -nr
```
<img width="786" height="83" alt="image" src="https://github.com/user-attachments/assets/479bff29-8e0e-4453-b861-b6d145a41631" />

```bash
sudo awk '$9 >= 400 { print $7 }' /var/log/apache2/access.log | sort | uniq -c | sort -nr
```
<img width="789" height="81" alt="image" src="https://github.com/user-attachments/assets/1fff1c48-c51d-47bd-bb8d-063c7949f26f" />

**Explanation:** These one-liners demonstrate why AWK is so useful for log analysis — tasks that would take 20 lines of Python take one line of AWK. `$1` = IP address, `$7` = URL requested, `$9` = status code in Apache's Combined Log Format.

---

## Linux Server Management Reference Scripts

### Create a user interactively

```bash
read -p "Enter username: " username
sudo useradd -m "$username"
sudo passwd "$username"
```
<img width="787" height="154" alt="image" src="https://github.com/user-attachments/assets/8c94575c-afce-46c3-bf31-71953d3f15f0" />

### Create a group and add a user

```bash
read -p "Group name: " group
read -p "User to add: " user
sudo groupadd "$group"
sudo usermod -aG "$group" "$user"
```
<img width="760" height="135" alt="image" src="https://github.com/user-attachments/assets/79905080-3b92-4f9c-a365-9a65238daf0a" />

### Top 10 memory-using processes

```bash
ps aux --sort=-%mem | head -n 11
```
<img width="797" height="83" alt="image" src="https://github.com/user-attachments/assets/949d3b44-efba-4e79-9dd5-f69eac4403c4" />

### Check open ports

```bash
ss -tuln
```
<img width="769" height="328" alt="image" src="https://github.com/user-attachments/assets/ecf4c7ab-e20b-42ec-a4cb-be4d31e5d240" />

### Disk alert if usage over 90%

```bash
df -h | grep '^/dev/' | awk '{print $5, $6}' | while read use mount; do
    use_num=${use%%%}
    if [ "$use_num" -gt 90 ]; then
        echo "WARNING: Disk usage alert: $mount at $use"
    fi
done
```

**Expected output:** Nothing if all disks are under 90%; a warning line for any that exceed it.

### Directory backup with date stamp

```bash
tar -czf /tmp/etc_backup_$(date +%F).tar.gz /etc
ls -lh /tmp/etc_backup_*.tar.gz
```
<img width="803" height="66" alt="image" src="https://github.com/user-attachments/assets/04149cdf-3683-45e3-b5bd-d879f2946215" />

**Explanation:** `$(date +%F)` inserts today's date in `YYYY-MM-DD` format into the filename, so each day's backup gets a unique name. This is a fundamental pattern in backup scripting — you'll build on this in Lab 3b.

### Set up a daily cron job

```bash
crontab -e
```

Add this line at the bottom:
```
0 1 * * * tar -czf /tmp/etc_backup_$(date +\%F).tar.gz /etc
```

```bash
crontab -l
```
<img width="800" height="49" alt="image" src="https://github.com/user-attachments/assets/d50585f9-ec49-4656-934a-2fff21122591" />


**Explanation:** Cron is Linux's task scheduler. The five fields before the command mean: `minute hour day-of-month month day-of-week`. So `0 1 * * *` = "at 1:00am every day". The `%` in `date` must be escaped as `\%` inside crontab.
