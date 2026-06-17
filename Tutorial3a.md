# Lab 3a – DNS, Domain Names & Let's Encrypt (HTTPS)
---

## 3a-1: DNS — Obtaining and Linking a Domain Name

### What is DNS and why do we need it?

Right now your website is only reachable via a raw IP address like `http://13.236.203.73` — not very memorable or professional. DNS (Domain Name System) is the internet's phone book: it translates human-readable names like `mysite.com` into IP addresses like `203.0.113.45`. This lab connects those two things.

---

### Step 1 – Confirm your EC2 instance is running

SSH into your EC2 server and confirm Apache is up:

```bash
ssh -i ~/Downloads/webserver-key.pem ubuntu@YOUR_EC2_IP
```

```bash
sudo systemctl status apache2
```
<img width="798" height="423" alt="image" src="https://github.com/user-attachments/assets/7e2ec8f0-ec90-4bf0-8e05-51a2374186a6" />

If it says `inactive`, start it:

```bash
sudo systemctl start apache2
```

Note your current public IP:

```bash
curl ifconfig.me
```
<img width="612" height="60" alt="image" src="https://github.com/user-attachments/assets/1019f3d5-8c5f-409a-b4e8-075ddc7bffcf" />
---

### Step 2 – Open port 443 in your AWS Security Group

Before getting a certificate, HTTPS traffic (port 443) must be allowed through your firewall. In the AWS console:

1. Click your instance → **Security** tab → click your security group <img width="1528" height="388" alt="image" src="https://github.com/user-attachments/assets/7f28d068-4262-4ea8-9187-2c2df29276b0" />

2. Click **Edit inbound rules → Add rule**
3. Type: **HTTPS**, Port: **443**, Source: **Anywhere (0.0.0.0/0)** <img width="1821" height="674" alt="image" src="https://github.com/user-attachments/assets/7988ce84-5b2a-4c84-9c98-98c2a74ffa35" />

4. Click **Save rules**

Your security group inbound rules should now show:

| Type | Port | Source |
|---|---|---|
| SSH | 22 | 0.0.0.0/0 |
| HTTP | 80 | 0.0.0.0/0 |
| HTTPS | 443 | 0.0.0.0/0 |

---

### Step 3 – Get a free domain name from DuckDNS

The lab allows any domain registrar (Namecheap, GoDaddy, Route 53 etc.), but **DuckDNS** is the easiest option for this unit — it's completely free, no credit card required, and takes 2 minutes to set up.

1. Go to https://www.duckdns.org
2. Log in with Google or GitHub
3. In the **"sub domain"** field, type a name of your choice 
4. Click **add domain** <img width="810" height="785" alt="image" src="https://github.com/user-attachments/assets/632788a9-6548-4df6-b77f-73b74da1d228" />

5. In the **"current ip"** field, type your EC2 public IP address
6. Click **update ip** <img width="587" height="148" alt="image" src="https://github.com/user-attachments/assets/6d3dbea1-2350-443f-9772-823c8fa792f3" />

**Explanation:** DuckDNS gives you a free subdomain under `duckdns.org`. An **A record** is the DNS record type that maps a domain name to an IPv4 address — you've just created one pointing `glennisea.duckdns.org` at your EC2 server's public IP.

---

### Step 4 – Wait for DNS to propagate and test

DNS changes can take a few minutes to propagate. Test from your **local Lubuntu VM** (not inside your EC2 SSH session):

```bash
nslookup glennisea.duckdns.org
```
<img width="684" height="163" alt="image" src="https://github.com/user-attachments/assets/0ba03a63-7087-4666-bfa9-84d9ded65754" />

The IP shown should match your EC2 public IP. If it doesn't match yet, wait 2–3 minutes and try again.

```bash
ping -c 4 glennisea.duckdns.org
```
<img width="794" height="310" alt="image" src="https://github.com/user-attachments/assets/441e3bb1-867e-461b-bb68-19a71d106db9" />

Now open Firefox and visit:
```
http://glennisea.duckdns.org
```
<img width="958" height="706" alt="image" src="https://github.com/user-attachments/assets/4a11cdeb-ad02-43a9-a801-e3dd60380db0" />

Also test SSH via domain name (on your local VM):

```bash
ssh -i ~/Downloads/webserver-key.pem ubuntu@glennisea.duckdns.org
```
<img width="799" height="292" alt="image" src="https://github.com/user-attachments/assets/e2d36a44-1c45-4d35-b357-0185a29bca74" />

**Explanation:** Both work because they resolve to the same IP. The domain name is just a human-readable alias — under the hood the SSH and HTTP traffic still goes to `203.0.113.45`.

---

### Step 5 – Experimenting (reflection tasks)

#### Who issued the certificates for murdoch.edu.au vs csn.murdoch.edu.au?

Open Firefox and visit both:
- https://murdoch.edu.au — click the **lock icon → Connection secure → More information → View Certificate**
- https://csn.murdoch.edu.au — same steps

Document who issued each certificate — they may be different Certificate Authorities (e.g. DigiCert, Sectigo, Let's Encrypt). This shows that different parts of the same organisation can use different CA providers.

#### What happens with two A records pointing to the same hostname?

In DuckDNS you can only have one A record per subdomain, but if you were using a full registrar you could test this.

- **Which one resolves?** DNS returns both IPs and the client usually picks whichever comes first in the response, which rotates (this is called **DNS round-robin load balancing**)
- **Does it failover if one server goes down?** No — DNS has no health checking. If one IP goes down, DNS still returns it and roughly half of clients will get a connection failure. Production systems use a proper load balancer (e.g. AWS ELB) rather than DNS round-robin for real failover
- **Why does it take so long to change?** DNS responses are **cached** by resolvers and clients for a period defined by the record's **TTL (Time To Live)**. Until the TTL expires, clients keep using the old answer even if you've changed the record

---

### Budgets reminder

Back in the AWS console, confirm your billing alert from Lab 2b is still set. If not:

1. AWS console → click your name (top right) → **Billing Dashboard**
2. **Budgets → Create budget → Cost budget**
3. Set amount: **$5 USD**, alert at **80%** with your email
4. **Create budget**

---

## 3a-2: Obtaining a Digital Certificate from Let's Encrypt (HTTPS)

### What is HTTPS and why does it matter?

Right now your website runs over **HTTP** — all traffic between your visitor's browser and your server is unencrypted. Anyone on the same network can read it. **HTTPS** adds a TLS certificate that encrypts the connection and proves your server's identity. The padlock icon in a browser means HTTPS is active.

**Let's Encrypt** is a free, automated Certificate Authority (CA) — it issues real trusted certificates at no cost. **Certbot** is the tool that requests and installs those certificates automatically.

---

### Step 1 – Pre-requisite checks

On your **local Lubuntu VM**, confirm your domain resolves and HTTP works:

```bash
wget http://glennisea.duckdns.org
```
<img width="793" height="272" alt="image" src="https://github.com/user-attachments/assets/e3523ca7-1e7b-48ea-a1df-66af142ea49f" />

If this fails, go back to 3a-1 and fix DNS before continuing — certbot needs HTTP working to verify you own the domain.

SSH into your EC2 server:

```bash
ssh -i ~/Downloads/webserver-key.pem ubuntu@glennisea.duckdns.org
```

Confirm ports 80 and 443 are open:
<img width="529" height="59" alt="image" src="https://github.com/user-attachments/assets/5bb6947b-236f-49a2-8836-c09fd6b7e957" />
<img width="544" height="124" alt="image" src="https://github.com/user-attachments/assets/abf50091-96a3-464f-841b-5bc4c80d1129" />

```bash
sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw enable
sudo ufw status
```
<img width="580" height="258" alt="image" src="https://github.com/user-attachments/assets/d202c121-b31f-4d85-a6ae-aefcc573d262" />

---

### Step 2 – Install Certbot via snap

All commands from here run **inside your EC2 SSH session**:

```bash
sudo snap install --classic certbot
```
<img width="683" height="42" alt="image" src="https://github.com/user-attachments/assets/5b1da95a-641d-4ee3-94fe-71b73f9a8c87" />

Create the certbot symlink so you can run it as a plain command:

```bash
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```

Verify certbot is installed:

```bash
certbot --version
```
<img width="496" height="43" alt="image" src="https://github.com/user-attachments/assets/8af50368-697c-4810-8791-8676bf3e87d5" />

---

### Step 3 – Run Certbot

```bash
sudo certbot --apache
```

Certbot will walk you through an interactive setup:

<img width="647" height="90" alt="image" src="https://github.com/user-attachments/assets/303ceeed-e8c7-4259-82d6-b21cee67ffff" />

Type your email address and press Enter

```
Please read the Terms of Service at https://letsencrypt.org/...
(A)gree/(C)ancel:
```
<img width="801" height="239" alt="image" src="https://github.com/user-attachments/assets/4735ad49-4330-4e2c-ae36-ebb4bff8b1fe" />

Type `Y` and press Enter
<img width="798" height="147" alt="image" src="https://github.com/user-attachments/assets/bfe83e94-f575-45ec-8332-b192f4c674cd" />

```
Would you be willing to share your email address with the EFF?
(Y)es/(N)o:
```
Type `N` and press Enter

```
Which names would you like to activate HTTPS for?
1: glennisea.duckdns.org
Select the appropriate numbers (Enter to select all):
```
Press **Enter** to select all
<img width="642" height="95" alt="image" src="https://github.com/user-attachments/assets/a5254d09-fc76-43d0-ac59-841f73d20f5d" />

<img width="794" height="465" alt="image" src="https://github.com/user-attachments/assets/83ba5403-bc80-45c4-9b13-572e49464cd9" />

**Explanation:** Certbot does several things automatically:
1. Requests a certificate from Let's Encrypt by proving you control the domain (it temporarily serves a challenge file from your Apache server)
2. Installs the certificate into Apache's config
3. Creates a new Apache virtual host for HTTPS (port 443)
4. Sets up automatic HTTP → HTTPS redirect

---

### Step 4 – Verify HTTPS is working

Open Firefox on your **local Lubuntu VM** and visit:
```
https://glennisea.duckdns.org
```
<img width="350" height="40" alt="image" src="https://github.com/user-attachments/assets/b6a8a80a-ace0-4dc7-9935-591c9cf17e51" />

Click the **padlock → Connection secure → More information → View Certificate**
<img width="463" height="385" alt="image" src="https://github.com/user-attachments/assets/eff27bcb-677f-4737-841c-bc0acefafaf3" />

### Step 5 – Confirm auto-renewal

Let's Encrypt certificates expire after 90 days. Certbot installs a timer that automatically renews them before expiry. Test it with a dry run:

```bash
sudo certbot renew --dry-run
```
<img width="793" height="378" alt="image" src="https://github.com/user-attachments/assets/965e9f34-4fe2-49b4-88b3-1ae1f25328cf" />

**Explanation:** The 90-day expiry is intentional — short-lived certificates limit the damage if a private key is ever compromised. Certbot's auto-renewal means you never have to manually renew it; it silently renews every ~60 days.

---

### What you have built — end to end

```
Browser types:  https://glennisea.duckdns.org
       ↓
DNS lookup:     glennisea.duckdns.org → 203.0.113.45  (your EC2 IP)
       ↓
TCP connection: Browser connects to 203.0.113.45:443
       ↓
TLS handshake:  Server presents Let's Encrypt certificate
                Browser verifies it's trusted → padlock appears
       ↓
Apache serves:  /var/www/html/index.html over encrypted HTTPS
```

This is the complete stack behind every `https://` website on the internet.
