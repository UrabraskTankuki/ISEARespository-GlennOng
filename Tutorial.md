# Lab 1a – Setting Up a Linux Environment & Ubuntu/CLI Familiarisation

## 1a-1: Setting Up Your Virtual Machine

1. Download **Lubuntu 24.04.2 (Noble Numbat) 64-bit** from https://lubuntu.me/downloads/
2. Install **Oracle VirtualBox** on your host machine
3. Create a new VM in VirtualBox, attach the Lubuntu `.iso`, and allocate at least 2GB RAM / 20GB disk
4. Run through the Ubuntu/Lubuntu installer, setting your username and password
5. Download guide: https://youtu.be/6Inw_7S2EBo

### Verify the install

Once you've booted into your new VM and opened a terminal, run:

```bash
lsb_release -a
```
<img width="766" height="638" alt="image" src="https://github.com/user-attachments/assets/35e61ea7-f17c-44b9-b01f-789a50965814" />

```bash
uname -a
```

<img width="804" height="634" alt="image" src="https://github.com/user-attachments/assets/5e8a7b88-3122-47c7-9d1f-fa72de02c73a" />


**Explanation:** `lsb_release -a` confirms the exact distribution and version you installed. `uname -a` shows the kernel version, architecture (x86_64), and machine name.

---

## 1a-2: Ubuntu Desktop and CLI Familiarisation

### GUI Familiarisation

* Open **Firefox** and confirm the internet works
  <img width="1014" height="805" alt="image" src="https://github.com/user-attachments/assets/b7b773ed-e73d-4934-aa0f-3e3f4a24f1d5" />
* Open **LibreOffice Writer** and type a short paragraph
  <img width="1264" height="889" alt="image" src="https://github.com/user-attachments/assets/9521fcc8-d84b-4f8d-9064-9e01f6658d23" />
* Open the **file manager** and navigate up/down a few folders
  <img width="637" height="517" alt="image" src="https://github.com/user-attachments/assets/69ae50f9-2a13-4705-939a-1dbdb2b6c4fd" />
  
**Explanation:** This section just confirms your desktop environment is functional before you move to the terminal-heavy parts of the lab.

---

### CLI Familiarisation

Open a terminal (Ctrl+Alt+T or via the app menu).

#### Process and resource monitoring

```bash
ps -e
```
<img width="795" height="638" alt="image" src="https://github.com/user-attachments/assets/c5244402-b61c-40fa-8e65-d929280afcfa" />

**Explanation:** Lists every running process with its Process ID (PID), the terminal it's attached to (TTY), CPU time used, and the command name. This is a snapshot, not a live view.

```bash
top
```
<img width="809" height="633" alt="image" src="https://github.com/user-attachments/assets/4e8f6da5-0dba-4e98-94ba-5acad12226a5" />

While `top` is running, press **1** — this toggles between an averaged CPU summary and a per-core breakdown (one line per CPU core). Press **q** to quit.

**Explanation:** `top` is the live version of `ps -e` — it refreshes every few seconds so you can watch resource usage change in real time.

#### Listing files

```bash
ls
```
<img width="567" height="69" alt="image" src="https://github.com/user-attachments/assets/1a0bbd11-2681-4b8d-abba-2d6738d653c9" />

```bash
ls -la
```

<img width="667" height="480" alt="image" src="https://github.com/user-attachments/assets/5acc2be8-7b7c-4e49-9535-a8d96e27a053" />

**Explanation:** `ls` only shows visible files/folders. `ls -la` adds the `-a` (all, including hidden "dotfiles" like `.bashrc`) and `-l` (long format, showing permissions, owner, group, size, and last-modified date) flags. This is your first look at file permissions, which becomes important in Lab 1b-2.

#### Creating and editing files

```bash
sudo apt install gedit
```
```bash
touch testfile
```
```bash
gedit testfile
```
<img width="907" height="751" alt="image" src="https://github.com/user-attachments/assets/3551b88c-22d6-4119-b82e-baded66ad6eb" />

```bash
nano testfile
```
<img width="801" height="629" alt="image" src="https://github.com/user-attachments/assets/2615e55b-6f7c-4d8d-bcd5-c9184a4b78e3" />

**Explanation:** `gedit`/`featherpad` are graphical (GUI) editors — they need a desktop environment and won't work over a plain SSH session. `nano` is a terminal-based editor that works anywhere, even on headless servers with no GUI at all. This is why server administrators rely on `nano`/`vim` rather than `gedit`.

#### Viewing files: `cat` vs `less`
Add text to the file and save
<img width="916" height="157" alt="image" src="https://github.com/user-attachments/assets/46c4cadb-c4a3-4871-8854-fbd93f6b3375" />

```bash
cat testfile
```
<img width="801" height="635" alt="image" src="https://github.com/user-attachments/assets/fc345a12-c1f5-4dce-aebb-9680901d2644" />

```bash
less testfile
```

<img width="803" height="633" alt="image" src="https://github.com/user-attachments/assets/3fcd9485-1186-4d68-adf6-0cd9920f9b91" />

**Explanation:** `cat` is best for short files or piping into other commands. `less` is better for long files — it loads the file without printing everything at once, and lets you search (`/keyword`) and scroll.

#### Copying and moving files

```bash
cp testfile testfile2
ls
```

<img width="803" height="636" alt="image" src="https://github.com/user-attachments/assets/b4bb5fa5-97d4-487c-ad53-824e74b17d40" />

```bash
mv testfile2 testfile3
ls
```
<img width="812" height="639" alt="image" src="https://github.com/user-attachments/assets/4fc95e98-9853-489d-9dc8-72a16f46007f" />

**Explanation:** `cp` duplicates a file — you end up with two copies. `mv` either renames a file (as shown here) or moves it to a different directory — the original disappears and only the new name/location remains.

#### Long listing with sort by size

```bash
ls -lah
```
<img width="807" height="643" alt="image" src="https://github.com/user-attachments/assets/2c6b1920-3279-4da9-9377-1f8342d3a6af" />

**Explanation:** The `-h` flag makes the `-l` long listing easier to read for humans — `4.0K` instead of `4096`.

#### System identification

```bash
uname -a
```
<img width="803" height="633" alt="image" src="https://github.com/user-attachments/assets/b598b1ad-ab70-4d4f-9514-2c976a1df5da" />

```bash
lsb_release -a
```
<img width="807" height="631" alt="image" src="https://github.com/user-attachments/assets/24a5a8b3-6603-4d08-a0ac-6949b7bbef79" />

```bash
hostnamectl
```
<img width="802" height="630" alt="image" src="https://github.com/user-attachments/assets/4a6be2c7-3ec7-4ff6-8c52-48827053a2bd" />

**Explanation:** `uname -a` shows kernel-level info. `lsb_release -a` shows distribution-level info (Ubuntu vs Debian vs Fedora etc.). `hostnamectl` combines both plus your hostname and confirms you're running inside a **virtual machine** (`Virtualization: oracle`) — a nice link back to the virtualisation concept from 1a-1.

#### Files sorted by modification time

```bash
ls -alt
```
<img width="749" height="488" alt="image" src="https://github.com/user-attachments/assets/260a4244-b388-40f2-81ba-82d8ed091fbf" />

**Explanation:** The `-t` flag sorts by modification time (newest first). This is handy when you've just edited or created a file and want to confirm it at the top of the list — useful for debugging "did my script actually save?" type questions.

---

### Super User

```bash
whoami
```
<img width="797" height="636" alt="image" src="https://github.com/user-attachments/assets/6dd923a3-32de-4581-b0ee-b875a772166f" />

```bash
sudo -i
adduser testuser
```
<img width="599" height="287" alt="image" src="https://github.com/user-attachments/assets/30b012ec-c956-4102-b85d-5698c922ba6f" />

```bash
sudo whoami
```

<img width="410" height="44" alt="image" src="https://github.com/user-attachments/assets/27c53ab8-174e-4772-b8a3-328584f62a6c" />

```bash
sudo adduser testuser
```

<img width="573" height="260" alt="image" src="https://github.com/user-attachments/assets/2eb66c8b-a6fd-413c-b105-2605ae125c09" />

```

**Explanation:** Regular users cannot create new accounts — only **root** (the Linux superuser, equivalent to "Administrator" on Windows) can. `sudo` ("superuser do") temporarily elevates your current user to root for a single command, after re-confirming your password. This "principle of least privilege" — staying as a normal user until you explicitly need elevated rights — is a core Linux security concept.

---

### Network Configuration

```bash
ip a
```

<img width="799" height="416" alt="image" src="https://github.com/user-attachments/assets/7747af48-2c2f-44a8-8bbd-0dc05116f377" />

**Explanation:** Shows every network interface and its assigned IP address(es). `lo` is the loopback interface (always `127.0.0.1`); `enp0s3` (or `eth0`) is your main network adapter, here with a private IP `192.168.1.50`.

```bash
ping 8.8.8.8
```
Press **Ctrl+C** to stop.

<img width="733" height="244" alt="image" src="https://github.com/user-attachments/assets/1903fec0-76b6-4b8e-8032-3be504aa07d0" />

**Explanation:** `8.8.8.8` is one of Google's public DNS servers — it's a reliable address to test that you have internet connectivity, since it almost always responds to pings.

#### Hosts file

```bash
less /etc/hosts
```

<img width="538" height="179" alt="image" src="https://github.com/user-attachments/assets/614bfc28-c4e5-49a2-a7ac-f8cb2bb1a23e" />

Press **q** to exit.

```bash
ping localhost
```
<img width="715" height="242" alt="image" src="https://github.com/user-attachments/assets/dda566fb-b0c5-4c2d-9b94-59995531c765" />

Now add a custom entry:

```bash
sudo nano /etc/hosts
```

Add this line at the bottom, save (Ctrl+O) and exit (Ctrl+X):
```
8.8.8.8 GoogleEpicDNS
```

<img width="801" height="630" alt="image" src="https://github.com/user-attachments/assets/26dc77aa-e736-431e-9f77-65fa3eed6f2c" />

```bash
ping GoogleEpicDNS
```
<img width="779" height="218" alt="image" src="https://github.com/user-attachments/assets/d3bc5386-e98c-402a-9149-9ae7d18eb744" />

**Explanation:** `/etc/hosts` is a simple local lookup table mapping names to IP addresses — checked *before* any DNS server is consulted. By adding `8.8.8.8 GoogleEpicDNS`, you've created a local "alias" so `ping GoogleEpicDNS` resolves to `8.8.8.8`. This is the same mechanism that lets `localhost` resolve to `127.0.0.1`.

#### DNS lookups

```bash
nslookup google.com
```
<img width="808" height="636" alt="image" src="https://github.com/user-attachments/assets/764790cb-9aba-4587-b0b7-e03b6eef714c" />

**Explanation:** Unlike `/etc/hosts` (local-only), DNS is a globally distributed lookup service. `nslookup` asks a DNS server to translate `google.com` into an IP address. You can paste that resulting IP address into your browser and it will load Google's homepage — proving DNS is "just" a name-to-IP lookup.

```bash
sudo apt install whois
whois google.com
```
<img width="803" height="637" alt="image" src="https://github.com/user-attachments/assets/074ec16a-1638-4c88-98a7-213bcf76515f" />

**Explanation:** `whois` queries domain registration records — useful for finding out who legally owns/administers a domain, and who to contact for abuse complaints.

#### Public vs Private IP addresses

```bash
ip a
```

Note your address (e.g. `127.0.0.1`) — this is a **private** IP, only valid inside your home/office network.

Now open Firefox and visit: https://whatismyipaddress.com/

<img width="1230" height="674" alt="image" src="https://github.com/user-attachments/assets/6eba95ed-66a6-44c6-9809-8c4b25046c49" />

**Explanation:** Your private IP is assigned by your router for use *within* your local network (many devices can share private ranges like `192.168.x.x`). Your public IP is the single address your router/ISP uses to represent your whole network to the outside internet — this is **Network Address Translation (NAT)** at work.

---

### Hardware Inspection

```bash
lsusb
```
<img width="723" height="87" alt="image" src="https://github.com/user-attachments/assets/e0177c23-fbc4-4fea-9122-ef0b24928c32" />

```bash
lspci
```
<img width="797" height="438" alt="image" src="https://github.com/user-attachments/assets/8831b0f6-5835-4b95-9467-d1f844b3f7b6" />

```bash
less /proc/cpuinfo
```
<img width="802" height="635" alt="image" src="https://github.com/user-attachments/assets/597bfc86-fb1d-4c51-8a43-2d604aa9bc18" />

**Explanation:** `lsusb` and `lspci` list devices connected via USB and the PCI bus respectively (in a VM, you'll mostly see virtual hardware). `/proc/cpuinfo` lists every CPU core the OS sees — count the `processor :` entries to see how many cores your VM has been given. Compare this with **Settings → About** in the GUI — the GUI usually gives a cleaner summary, but the CLI gives you the raw detail (e.g. exact CPU model and clock speed).

---

### Redirecting Output to a File

```bash
lsusb > output_of_lsusb
```
```bash
less output_of_lsusb
cat output_of_lsusb
```
<img width="703" height="92" alt="image" src="https://github.com/user-attachments/assets/c67fc714-3984-455f-a9d6-1ad7f6bd5d98" />
<img width="699" height="108" alt="image" src="https://github.com/user-attachments/assets/93e54516-2393-4ebd-ac45-1e551fa7b65f" />

```bash
ls -la output_of_lsusb
```
<img width="639" height="37" alt="image" src="https://github.com/user-attachments/assets/fe47ecc5-9b58-460e-b662-35979fe3a64e" />

```bash
rm output_of_lsusb
```
<img width="486" height="22" alt="image" src="https://github.com/user-attachments/assets/f3572f2e-097e-4434-bef0-1762c13be473" />
file is removed

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

