<img width="799" height="532" alt="image" src="https://github.com/user-attachments/assets/cff9cb90-4b63-44a5-9d7c-81ccb27628db" /># **Session 4**

# *Session 4a*

## Additional Server Services

Linux servers can run a wide range of software services beyond just a web server. Services are grouped into three tiers based on complexity:
- 🟩 Simple: single-purpose, easy to install
- 🟨 Medium: multi-tier apps with databases and dashboards
- 🟥 Complex: enterprise stacks and containers

---

## 🟩 Simple Services

### Web Server (Apache) — already installed ✅

- sudo systemctl status apache2: checks if Apache is running
- sudo systemctl start apache2: starts Apache if stopped

Visit http://localhost in Firefox to confirm it's working

### Monitoring (htop)

- sudo apt install htop -y: installs htop process monitor
- htop: opens the live monitoring dashboard, press q to exit

<img width="799" height="532" alt="image" src="https://github.com/user-attachments/assets/60387d9d-3cd5-4c64-8de1-2298ac29b2e5" />
htop shows CPU usage, memory usage, and all running processes in a colour-coded display — easier to read than top

- sudo apt install glances -y: installs glances for a more detailed dashboard
- glances: opens full terminal dashboard showing CPU, memory, disk, and network all at once, press q to exit
<img width="807" height="538" alt="image" src="https://github.com/user-attachments/assets/4bc921ac-3e67-464b-a5e3-c0bc264a55be" />

### Security (Fail2ban)

- sudo apt install fail2ban -y: installs fail2ban intrusion prevention
- sudo systemctl status fail2ban: confirms fail2ban is running

- sudo fail2ban-client status: shows which jails are active
<img width="792" height="326" alt="image" src="https://github.com/user-attachments/assets/08229b68-79db-4a86-ba08-eef2e9d561d0" />

fail2ban watches log files and automatically bans IP addresses that make too many failed login attempts — protects against brute force attacks on SSH

### DNS Tools

- sudo apt install dnsutils -y: installs DNS lookup tools
- nslookup google.com: resolves google.com to its IP address using DNS
- dig murdoch.edu.au: shows detailed DNS response including TTL values and record types

dig is the more detailed version of nslookup — standard tool for debugging DNS problems

### FTP Server (vsftpd)

- sudo apt install vsftpd -y: installs vsftpd FTP server
- sudo systemctl status vsftpd: confirms vsftpd is running
- ftp localhost: connects to local FTP server
<img width="803" height="475" alt="image" src="https://github.com/user-attachments/assets/7445ac57-c3ac-4b0a-ac26-efbf542e4a12" />

Type your username and password, then ls to list files and bye to exit

vsftpd transmits data unencrypted — for real use, SFTP (over SSH) is preferred
<img width="427" height="165" alt="image" src="https://github.com/user-attachments/assets/bd768da8-1df3-40dc-9378-98cea68a5a5d" />

### SSH Server — already running ✅

- sudo systemctl status ssh: confirms SSH is running
- ssh localhost: connects to your own machine via SSH
<img width="795" height="542" alt="image" src="https://github.com/user-attachments/assets/60899965-27d0-4e0d-af2f-69eb64785429" />

Type exit when done

### Database (SQLite)

- sqlite3 --version: confirms SQLite is already installed
<img width="792" height="61" alt="image" src="https://github.com/user-attachments/assets/08b4390e-f858-442e-af35-c98adba9e321" />

- sqlite3 ~/testdb.db: creates and opens a test database

Inside the SQLite prompt:
```sql
CREATE TABLE students (id INTEGER PRIMARY KEY, name TEXT, grade TEXT);
INSERT INTO students VALUES (1, 'Glenn', 'A');
INSERT INTO students VALUES (2, 'Alice', 'B');
SELECT * FROM students;
.quit
```

Expected output:
```
1|Glenn|A
2|Alice|B
```

SQLite stores an entire database as a single file — no server process needed. Used by Android, Firefox, and thousands of apps as an embedded database

---
