# Cloudflare Tunnel

Cloudflare Tunnel allows a local server to be accessed from the Internet without requiring a public IP address or port forwarding.

This is useful when the server is behind:

- 4G/5G router
- CGNAT
- Home Internet without a public IP
- Office network
- Local/private network

---

# Basic Architecture

```text
Internet
    │
    ▼
Cloudflare
    │
    ▼
Cloudflare Tunnel
    │
    ▼
cloudflared
    │
    ▼
Local Server
```

The local server creates an **outbound connection** to Cloudflare.

Therefore, the router does not need to expose port `80` or `443` to the Internet.

---

# 1. Install Cloudflared

For Ubuntu/Debian, download the package:

```bash
cd /tmp

wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
```

Install:

```bash
dpkg -i cloudflared-linux-amd64.deb
```

Check:

```bash
cloudflared --version
```

Example:

```text
cloudflared version 2026.8.2
```

---

# 2. Quick Tunnel

Quick Tunnel is useful for testing.

It does not require:

- Cloudflare account
- Custom domain
- DNS configuration

Start a local web server first.

Example:

```bash
python3 -m http.server 8080
```

Then start the Quick Tunnel:

```bash
cloudflared tunnel --url http://localhost:8080
```

If the network has problems with QUIC, use HTTP/2:

```bash
cloudflared tunnel --protocol http2 --url http://localhost:8080
```

Cloudflare will provide a temporary URL:

```text
https://random-name.trycloudflare.com
```

Open this URL from another device, for example a phone using mobile data.

---

# Quick Tunnel Architecture

```text
Phone / Internet
       │
       ▼
Cloudflare
       │
       ▼
random-name.trycloudflare.com
       │
       ▼
cloudflared
       │
       ▼
localhost:8080
       │
       ▼
Python Web Server
```

---

# Quick Tunnel Characteristics

Quick Tunnel is mainly for testing.

```text
Cloudflare Account     Not required
Custom Domain          Not required
DNS Configuration      Not required
URL                    Temporary
Production Website     Not recommended
```

If the tunnel is stopped:

```text
Ctrl+C
```

the temporary URL becomes unavailable.

Starting another Quick Tunnel normally creates another temporary URL.

Example:

```text
First:
https://abc123.trycloudflare.com

Stop tunnel

Second:
https://xyz789.trycloudflare.com
```

---

# 3. Custom Domain / Named Tunnel

For a real website, use a **Named Tunnel** associated with your Cloudflare account.

Example:

```text
test.example.com
```

The architecture becomes:

```text
Internet
    │
    ▼
test.example.com
    │
    ▼
Cloudflare
    │
    ▼
Named Tunnel
    │
    ▼
cloudflared
    │
    ▼
localhost:8080
```

---

# 4. Login to Cloudflare

Run:

```bash
cloudflared tunnel login
```

A browser will open.

Log in to the Cloudflare account that manages your domain.

Select the domain/zone you want to use.

After successful authentication, Cloudflared stores an authentication certificate under:

```text
~/.cloudflared/
```

Check:

```bash
ls -la ~/.cloudflared/
```

---

# 5. Create a Named Tunnel

Example:

```bash
cloudflared tunnel create home-server
```

Cloudflare will create a tunnel and a credential file.

Example:

```text
Tunnel UUID:
12345678-abcd-1234-abcd-123456789abc
```

The credential file will normally be:

```text
~/.cloudflared/12345678-abcd-1234-abcd-123456789abc.json
```

Check:

```bash
ls -la ~/.cloudflared/
```

---

# 6. Check Existing Tunnels

```bash
cloudflared tunnel list
```

Example:

```text
NAME          UUID                                  CREATED
home-server   12345678-abcd-1234-abcd-123456789abc  ...
```

The tunnel UUID normally matches the credential filename:

```text
12345678-abcd-1234-abcd-123456789abc.json
```

---

# 7. Create Tunnel Configuration

Create:

```bash
nano ~/.cloudflared/config.yml
```

Example:

```yaml
tunnel: 12345678-abcd-1234-abcd-123456789abc
credentials-file: /root/.cloudflared/12345678-abcd-1234-abcd-123456789abc.json

ingress:
  - hostname: test.example.com
    service: http://localhost:8080

  - service: http_status:404
```

Change:

```text
test.example.com
```

to your actual domain/subdomain.

Change the tunnel UUID and credential filename to your actual values.

---

# 8. Create DNS Route

Connect the hostname to the tunnel:

```bash
cloudflared tunnel route dns home-server test.example.com
```

Cloudflare will create the appropriate DNS record.

You do not need to point the domain directly to your home/office/4G IP.

---

# 9. Start the Named Tunnel

Normal:

```bash
cloudflared tunnel run home-server
```

If the network has problems with QUIC, use HTTP/2:

```bash
cloudflared tunnel --protocol http2 run home-server
```

---

# 10. Test the Custom Domain

Open:

```text
https://test.example.com
```

from another Internet connection.

For example:

```text
Phone
  │
  └── Mobile Data (4G/5G)
          │
          ▼
      Cloudflare
          │
          ▼
    test.example.com
          │
          ▼
     cloudflared
          │
          ▼
    localhost:8080
```

---

# Quick Tunnel vs Named Tunnel

| Feature | Quick Tunnel | Named Tunnel |
|---|---|---|
| Cloudflare account | Not required | Required |
| Custom domain | No | Yes |
| URL | `trycloudflare.com` | Your domain |
| URL changes | Normally yes | No |
| DNS configuration | No | Yes |
| Production website | Not recommended | Yes |
| Testing | Excellent | Excellent |

---

# Example: WordPress

Once WordPress is running through Nginx:

```text
Nginx
   │
   ▼
localhost:80
```

The Cloudflare configuration can be:

```yaml
tunnel: 12345678-abcd-1234-abcd-123456789abc
credentials-file: /root/.cloudflared/12345678-abcd-1234-abcd-123456789abc.json

ingress:
  - hostname: www.example.com
    service: http://localhost:80

  - service: http_status:404
```

Then:

```text
www.example.com
       │
       ▼
   Cloudflare
       │
       ▼
 Cloudflare Tunnel
       │
       ▼
  cloudflared
       │
       ▼
   Nginx :80
       │
       ▼
   WordPress
```

---

# Example: Multiple Websites

One tunnel can route multiple hostnames.

```yaml
tunnel: 12345678-abcd-1234-abcd-123456789abc
credentials-file: /root/.cloudflared/12345678-abcd-1234-abcd-123456789abc.json

ingress:
  - hostname: www.example.com
    service: http://localhost:80

  - hostname: api.example.com
    service: http://localhost:3000

  - hostname: test.example.com
    service: http://localhost:8080

  - service: http_status:404
```

Architecture:

```text
Cloudflare
    │
    ▼
cloudflared
    │
    ├── www.example.com
    │       └── localhost:80
    │
    ├── api.example.com
    │       └── localhost:3000
    │
    └── test.example.com
            └── localhost:8080
```

---

# Running Cloudflared with PM2

Cloudflared can also be managed by PM2.

Example:

```bash
pm2 start cloudflared --name cloudflare-tunnel -- tunnel --protocol http2 run home-server
```

Check:

```bash
pm2 status
```

View logs:

```bash
pm2 logs cloudflare-tunnel
```

Restart:

```bash
pm2 restart cloudflare-tunnel
```

Save:

```bash
pm2 save
```

---

# Important Security Notes

Do not upload Cloudflare credentials to GitHub.

Do NOT commit:

```text
*.json
cert.pem
```

Add them to `.gitignore`:

```gitignore
# Cloudflare Tunnel credentials
.cloudflared/
*.pem
```

Do not put API tokens, tunnel tokens, passwords, or private credentials in Markdown files.

Example configuration is safe:

```yaml
tunnel: YOUR-TUNNEL-UUID
credentials-file: /path/to/YOUR-TUNNEL-UUID.json
```

Use placeholders instead of real credentials.

---

# Current Test Result

Tested successfully with:

```text
WSL1
   │
   ▼
Python HTTP Server :8080
   │
   ▼
Cloudflare Quick Tunnel
   │
   ▼
Cloudflare Named Tunnel
   │
   ▼
Custom Domain
```

The network did not work correctly with QUIC, so HTTP/2 was used:

```bash
cloudflared tunnel --protocol http2 run home-server
```

This setup does not require a public IP or port forwarding.

---

# Future Local Server

The planned architecture is:

```text
4G Router + SIM
       │
       │ No public IP required
       ▼
Linux Mini PC
       │
       ├── Nginx
       │     ├── WordPress
       │     └── Laravel
       │
       ├── MariaDB
       │
       ├── MQTT
       │
       └── cloudflared
               │
               ▼
           Cloudflare
               │
               ▼
            Internet
```

Cloudflare Tunnel handles the HTTP/HTTPS websites.

MQTT should be considered separately because normal Cloudflare website proxying is not the same as exposing an MQTT broker on port `1883`.