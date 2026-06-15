# Lab 1a – Setting Up a Linux Environment & Ubuntu/CLI Familiarisation

This guide covers **Lab 1a-1 (Obtaining a Linux Environment)** and **Lab 1a-2 (Ubuntu Desktop and Command Line Familiarisation)**. For each task you'll find the command(s) to run, the kind of output you should expect, and a short explanation of what is happening and why. Take screenshots of your terminal at each step — that's the evidence you'll paste into your GitHub README.

---

## 1a-1: Setting Up Your Virtual Machine

This part is mostly GUI-based (no terminal commands), but document each step with a screenshot:

1. Download **Lubuntu 24.04.2 (Noble Numbat) 64-bit** from https://lubuntu.me/downloads/
2. Install **Oracle VirtualBox** on your host machine
3. Create a new VM in VirtualBox, attach the Lubuntu `.iso`, and allocate at least 2GB RAM / 20GB disk
4. Run through the Ubuntu/Lubuntu installer, setting your username and password

### Verify the install

Once you've booted into your new VM and opened a terminal, run:

```bash
lsb_release -a
```

**Expected output:**
```
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 24.04.2 LTS
Release:        24.04
Codename:       noble
```

```bash
uname -a
```

**Expected output (example):**
```
Linux yourname-VirtualBox 6.8.0-31-generic #31-Ubuntu SMP PREEMPT_DYNAMIC x86_64 GNU/Linux
```

**Explanation:** `lsb_release -a` confirms the exact distribution and version you installed. `uname -a` shows the kernel version, architecture (x86_64), and machine name — useful for confirming your VM is set up correctly before you start the rest of the lab.

---

## 1a-2: Ubuntu Desktop and CLI Familiarisation

### GUI Familiarisation (no terminal needed)

Document these with screenshots — no commands required:
* Open **Firefox** and confirm the internet works
* Open **LibreOffice Writer** and type a short paragraph
* Open the **file manager** and navigate up/down a few folders
* Open the **Ubuntu/Discover Software Centre** and install any one program (e.g. GIMP)

**Explanation:** This section just confirms your desktop environment is functional before you move to the terminal-heavy parts of the lab.

---

### CLI Familiarisation

Open a terminal (Ctrl+Alt+T or via the app menu).

#### Process and resource monitoring

```bash
ps -e
```

**Expected output (truncated example):**
```
    PID TTY          TIME CMD
      1 ?        00:00:01 systemd
      2 ?        00:00:00 kthreadd
   1432 ?        00:00:00 gnome-shell
   2210 pts/0    00:00:00 bash
   2255 pts/0    00:00:00 ps
```

**Explanation:** Lists every running process with its Process ID (PID), the terminal it's attached to (TTY), CPU time used, and the command name. This is a snapshot, not a live view.

```bash
top
```

**Expected output:** A full-screen, live-updating table of processes, sorted by CPU usage by default, with a summary header showing CPU/memory load.

While `top` is running, press **1** — this toggles between an averaged CPU summary and a per-core breakdown (one line per CPU core). Press **q** to quit.

**Explanation:** `top` is the live version of `ps -e` — it refreshes every few seconds so you can watch resource usage change in real time.

#### Listing files

```bash
ls
```

**Expected output:**
```
Desktop  Documents  Downloads  Music  Pictures  Public  Templates  Videos
```

```bash
ls -la
```

**Expected output:**
```
total 44
drwxr-x--- 16 user user 4096 Jun 15 09:00 .
drwxr-xr-x  3 root root 4096 Jun 15 08:50 ..
-rw-r--r--  1 user user  220 Jun 15 08:50 .bash_logout
-rw-r--r--  1 user user 3771 Jun 15 08:50 .bashrc
drwxr-xr-x  2 user user 4096 Jun 15 09:00 Desktop
drwxr-xr-x  2 user user 4096 Jun 15 09:00 Documents
...
```

**Explanation:** `ls` only shows visible files/folders. `ls -la` adds the `-a` (all, including hidden "dotfiles" like `.bashrc`) and `-l` (long format, showing permissions, owner, group, size, and last-modified date) flags. This is your first look at file permissions, which becomes important in Lab 1b-2.

#### Creating and editing files

```bash
touch testfile
```

**Expected output:** No output — the command succeeds silently and creates an empty file called `testfile`.

```bash
gedit testfile
```
*(or, if gedit isn't installed: `sudo apt install featherpad` then `featherpad testfile`)*

**Expected output:** A graphical text editor window opens. Type a paragraph, save, and close.

```bash
nano testfile
```

**Expected output:** The terminal switches to nano's editor view, showing your text with a menu bar of shortcuts (e.g. `^X Exit`, `^O Write Out`) at the bottom. Press **Ctrl+X** to exit.

**Explanation:** `gedit`/`featherpad` are graphical (GUI) editors — they need a desktop environment and won't work over a plain SSH session. `nano` is a terminal-based editor that works anywhere, even on headless servers with no GUI at all. This is why server administrators rely on `nano`/`vim` rather than `gedit`.

#### Viewing files: `cat` vs `less`

```bash
cat testfile
```

**Expected output:** The entire contents of `testfile` dumped immediately to the terminal.

```bash
less testfile
```

**Expected output:** A scrollable, page-by-page view of the file. Press **q** to exit.

**Explanation:** `cat` is best for short files or piping into other commands. `less` is better for long files — it loads the file without printing everything at once, and lets you search (`/keyword`) and scroll.

#### Copying and moving files

```bash
cp testfile testfile2
ls
```

**Expected output:**
```
testfile  testfile2
```

```bash
mv testfile2 testfile3
ls
```

**Expected output:**
```
testfile  testfile3
```

**Explanation:** `cp` duplicates a file — you end up with two copies. `mv` either renames a file (as shown here) or moves it to a different directory — the original disappears and only the new name/location remains.

#### Long listing with sort by size

```bash
ls -lah
```

**Expected output:** Same as `ls -la` but file sizes are shown in human-readable units (K, M, G) instead of raw bytes.

**Explanation:** The `-h` flag makes the `-l` long listing easier to read for humans — `4.0K` instead of `4096`.

#### System identification

```bash
uname -a
```

**Expected output:**
```
Linux yourname-VirtualBox 6.8.0-31-generic #31-Ubuntu SMP PREEMPT_DYNAMIC x86_64 x86_64 x86_64 GNU/Linux
```

```bash
lsb_release -a
```

**Expected output:**
```
Distributor ID: Ubuntu
Description:    Ubuntu 24.04.2 LTS
Release:        24.04
Codename:       noble
```

```bash
hostnamectl
```

**Expected output:**
```
 Static hostname: yourname-VirtualBox
       Icon name: computer-vm
         Chassis: vm
      Machine ID: 8f3a...
         Boot ID: 4b2c...
  Virtualization: oracle
Operating System: Ubuntu 24.04.2 LTS
          Kernel: Linux 6.8.0-31-generic
    Architecture: x86-64
```

**Explanation:** `uname -a` shows kernel-level info. `lsb_release -a` shows distribution-level info (Ubuntu vs Debian vs Fedora etc.). `hostnamectl` combines both plus your hostname and confirms you're running inside a **virtual machine** (`Virtualization: oracle`) — a nice link back to the virtualisation concept from 1a-1.

#### Files sorted by modification time

```bash
ls -alt
```

**Expected output:** Same as `ls -la`, but rows are reordered so the **most recently modified file/folder appears first**.

**Explanation:** The `-t` flag sorts by modification time (newest first). This is handy when you've just edited or created a file and want to confirm it at the top of the list — useful for debugging "did my script actually save?" type questions.

---

### Super User

```bash
whoami
```

**Expected output:**
```
yourname
```

```bash
adduser testuser
```

**Expected output:**
```
adduser: Only root can add a user or group to the system.
```

```bash
sudo whoami
```

**Expected output:** (after entering your password)
```
root
```

```bash
sudo adduser testuser
```

**Expected output:**
```
Adding user `testuser' ...
Adding new group `testuser' (1001) ...
Adding new user `testuser' (1001) with group `testuser' ...
Creating home directory `/home/testuser' ...
Copying files from `/etc/skel' ...
New password:
Retype new password:
passwd: password updated successfully
Changing the user information for testuser
Enter the new value, or press ENTER for the default
        Full Name []:
        ...
Is the information correct? [Y/n] y
```

**Explanation:** Regular users cannot create new accounts — only **root** (the Linux superuser, equivalent to "Administrator" on Windows) can. `sudo` ("superuser do") temporarily elevates your current user to root for a single command, after re-confirming your password. This "principle of least privilege" — staying as a normal user until you explicitly need elevated rights — is a core Linux security concept.

---

### Network Configuration

```bash
ip a
```

**Expected output (truncated example):**
```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 ...
    inet 127.0.0.1/8 scope host lo
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 ...
    inet 192.168.1.50/24 brd 192.168.1.255 scope global dynamic enp0s3
```

**Explanation:** Shows every network interface and its assigned IP address(es). `lo` is the loopback interface (always `127.0.0.1`); `enp0s3` (or `eth0`) is your main network adapter, here with a private IP `192.168.1.50`.

```bash
ping 8.8.8.8
```
Press **Ctrl+C** to stop.

**Expected output:**
```
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=115 time=14.2 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=115 time=13.8 ms
^C
--- 8.8.8.8 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
```

**Explanation:** `8.8.8.8` is one of Google's public DNS servers — it's a reliable address to test that you have internet connectivity, since it almost always responds to pings.

#### Hosts file

```bash
less /etc/hosts
```

**Expected output:**
```
127.0.0.1       localhost
127.0.1.1       yourname-VirtualBox
::1     localhost ip6-localhost ip6-loopback
```
Press **q** to exit.

```bash
ping localhost
```

**Expected output:**
```
PING localhost (127.0.0.1) 56(84) bytes of data.
64 bytes from localhost (127.0.0.1): icmp_seq=1 ttl=64 time=0.034 ms
```

Now add a custom entry:

```bash
sudo nano /etc/hosts
```

Add this line at the bottom, save (Ctrl+O) and exit (Ctrl+X):
```
8.8.8.8 GoogleEpicDNS
```

```bash
ping GoogleEpicDNS
```

**Expected output:**
```
PING GoogleEpicDNS (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=115 time=14.0 ms
```

**Explanation:** `/etc/hosts` is a simple local lookup table mapping names to IP addresses — checked *before* any DNS server is consulted. By adding `8.8.8.8 GoogleEpicDNS`, you've created a local "alias" so `ping GoogleEpicDNS` resolves to `8.8.8.8`. This is the same mechanism that lets `localhost` resolve to `127.0.0.1`.

#### DNS lookups

```bash
nslookup google.com
```

**Expected output:**
```
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
Name:   google.com
Address: 142.250.66.110
```

**Explanation:** Unlike `/etc/hosts` (local-only), DNS is a globally distributed lookup service. `nslookup` asks a DNS server to translate `google.com` into an IP address. You can paste that resulting IP address into your browser and it will load Google's homepage — proving DNS is "just" a name-to-IP lookup.

```bash
sudo apt install whois
whois google.com
```

**Expected output (truncated):**
```
   Domain Name: GOOGLE.COM
   Registrar: MarkMonitor Inc.
   Registrant Organization: Google LLC
   Registrar Abuse Contact Email: abusecomplaints@markmonitor.com
   ...
```

**Explanation:** `whois` queries domain registration records — useful for finding out who legally owns/administers a domain, and who to contact for abuse complaints.

#### Public vs Private IP addresses

```bash
ip a
```

Note your address (e.g. `192.168.1.50`) — this is a **private** IP, only valid inside your home/office network.

Now open Firefox and visit: https://whatismyipaddress.com/

**Expected output:** A different address is shown (e.g. `203.0.113.45`) — this is your **public** IP, as seen from the internet.

**Explanation:** Your private IP is assigned by your router for use *within* your local network (many devices can share private ranges like `192.168.x.x`). Your public IP is the single address your router/ISP uses to represent your whole network to the outside internet — this is **Network Address Translation (NAT)** at work.

---

### Hardware Inspection

```bash
lsusb
```

**Expected output:**
```
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 002 Device 002: ID 80ee:0021 VirtualBox USB Tablet
```

```bash
lspci
```

**Expected output (truncated):**
```
00:00.0 Host bridge: Intel Corporation 440FX - 82441FX PMC [Natoma]
00:01.0 ISA bridge: Intel Corporation 82371SB PIIX3 ISA [Natoma/Triton II]
00:02.0 VGA compatible controller: InnoTek Systemberatung GmbH VirtualBox Graphics Adapter
00:03.0 Ethernet controller: Intel Corporation 82540EM Gigabit Ethernet Controller
```

```bash
less /proc/cpuinfo
```

**Expected output:** One block per CPU core, e.g.:
```
processor       : 0
vendor_id       : GenuineIntel
model name      : Intel(R) Core(TM) i7-10700K CPU @ 3.80GHz
cpu MHz         : 3792.000
...
processor       : 1
...
```

**Explanation:** `lsusb` and `lspci` list devices connected via USB and the PCI bus respectively (in a VM, you'll mostly see virtual hardware). `/proc/cpuinfo` lists every CPU core the OS sees — count the `processor :` entries to see how many cores your VM has been given. Compare this with **Settings → About** in the GUI — the GUI usually gives a cleaner summary, but the CLI gives you the raw detail (e.g. exact CPU model and clock speed).

---

### Redirecting Output to a File

```bash
lsusb > output_of_lsusb
```

**Expected output:** Nothing printed to the screen — the output of `lsusb` goes into the new file `output_of_lsusb` instead.

```bash
less output_of_lsusb
cat output_of_lsusb
```

**Expected output:** Same content as running `lsusb` directly, but now read from the file (`cat` dumps it all at once, `less` lets you scroll).

```bash
ls -la output_of_lsusb
```

**Expected output:**
```
-rw-rw-r-- 1 user user 123 Jun 15 09:30 output_of_lsusb
```

```bash
rm output_of_lsusb
```

**Expected output:** Nothing — the file is silently deleted.

**Explanation:** The `>` operator redirects a command's standard output into a file (overwriting it if it exists). This is fundamental for logging, automation, and scripting — instead of a human reading terminal output, a program can capture it for later analysis.

---

### Installing Software

#### Software as a Service (no commands)
Discuss/note examples like Office 365, Google Docs, Grammarly — software that runs entirely in the browser, with no installation on any OS.

#### Downloading and installing a binary from the web

1. Open Firefox, go to google.com/chrome, download the `.deb` package
2. Install it:

```bash
sudo apt install ~/Downloads/google-chrome-stable_current_amd64.deb
```

**Expected output:** A long list of dependencies being fetched and configured, ending with:
```
Setting up google-chrome-stable (125.0.6422.141-1) ...
```

**Explanation:** A `.deb` file is a pre-built binary package for Debian/Ubuntu-based systems — the equivalent of a Windows `.exe`/installer or a macOS `.dmg`. Using `apt install` on a local `.deb` file also automatically resolves and installs any missing dependencies, which a plain double-click often can't do.

#### Installing from a trusted repository

```bash
less /etc/apt/sources.list
```

**Expected output:** A list of repository URLs, e.g.:
```
deb http://archive.ubuntu.com/ubuntu/ noble main restricted
deb http://archive.ubuntu.com/ubuntu/ noble-updates main restricted
```

```bash
sudo apt update
```

**Expected output:**
```
Hit:1 http://archive.ubuntu.com/ubuntu noble InRelease
Get:2 http://archive.ubuntu.com/ubuntu noble-updates InRelease [126 kB]
...
Reading package lists... Done
```

```bash
sudo apt upgrade
```

**Expected output:**
```
The following packages will be upgraded:
  firefox libssl3 ...
2 upgraded, 0 newly installed, 0 to remove.
Do you want to continue? [Y/n]
```

```bash
sudo apt search vlc
```

**Expected output (truncated):**
```
vlc/noble 3.0.20-1 amd64
  multimedia player and streamer
```

```bash
sudo apt install vlc
```

**Expected output:** Package download/install progress, ending with:
```
Setting up vlc (3.0.20-1) ...
```

**Explanation:** `/etc/apt/sources.list` tells your system *where* to look for software (Ubuntu's official repositories, which are vetted and trusted). `apt update` refreshes the local list of what's available; `apt upgrade` installs newer versions of already-installed packages. `apt search`/`apt install` find and install new software — this is the command-line equivalent of the Ubuntu Software Centre GUI, and it's the method used on servers that have no GUI at all.

#### Installing from source code

```bash
sudo apt install build-essential
```

**Expected output:** Installs `gcc`, `make`, and related compiler tools.

Create the file:

```bash
nano hello_world.c
```

Paste in:
```c
#include <stdio.h>
int main() {
   printf("Hello, World!\n");
   return 0;
}
```

Save and exit (Ctrl+O, Ctrl+X), then compile and run:

```bash
gcc hello_world.c -o hello_world_executable
./hello_world_executable
```

**Expected output:**
```
Hello, World!
```

If you get a permissions error:

```bash
chmod 777 hello_world_executable
```

Now compare the two files:

```bash
less hello_world.c
less hello_world_executable
```

**Expected output:** `hello_world.c` shows readable C source code. `hello_world_executable` shows mostly unreadable binary/machine code (garbled characters), possibly with a warning like `"hello_world_executable" may be a binary file. See it anyway?`.

**Explanation:** `build-essential` provides the compiler toolchain. `gcc` translates (compiles) human-readable C source code into a binary executable that the CPU can run directly. Comparing the two files with `less` visually demonstrates the difference between **source code** (what developers write) and **machine code** (what the computer actually executes) — this is "installing from source," as opposed to downloading a pre-compiled binary.

---

### Reflection

Write a short paragraph (no commands needed) comparing the four software installation methods covered above:
1. **Software as a Service** — zero installation, but you depend entirely on the provider and an internet connection
2. **Downloading a binary (.deb/.exe/.dmg)** — quick, but no automatic updates or dependency checks unless you use a package manager
3. **Installing from a trusted repository (apt)** — vetted, easy updates, dependency resolution handled for you
4. **Compiling from source** — maximum flexibility/transparency (you can read the code), but slower and requires build tools

Consider this from two perspectives: as an everyday **user**, and as a **software development company** deciding how to distribute their product.
