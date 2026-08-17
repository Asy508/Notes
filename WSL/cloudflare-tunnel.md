# WSL + Cloudflare Tunnel Quick Test

## 1. Install/check WSL
* Using PowerShell (Administrator) Install
```PowerShell
wsl --install
```
* Check
```
wsl -l -v
```
* Output: 
```
Ubuntu	Running	1/2
```

## 2. Enter Ubuntu
* From PowerShell
```PowerShell
wsl
```
* Output:
```
root@PC:~#
```Linux
* Now, we're inside Linux

## 3. Create Simple website
* Inside Ubuntu
```Linux
mkdir -p /root/test-web
cd /root/test-web
```
* Create a page
```Linux
echo '<h1>Hello world welcom to WSL CloudTunnel</h1>' > index.html
```
## 4. Start web server
```Linux
python3 -m http.server 8080
```
* Output:
```Linux
Serving HTTP on 0.0.0.0 port 8080
```
* Keep this terminal running
## 5. Test Locally
* Open another PowerShell
```PowerShell
curl http://localhost:8080
```
* We should see our HTML
## 6. Install Cloudflared
* Open another WSL terminal
```Linux
cd /tmp
```
* Download
```Linux
wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
```
* Install:
```Linux
dpkg -i cloudflared-linux-amd64.deb
```Linux
* Check:
```Linux
cloudflared --version
```

## 7. Start Quick Tunnel
```Linux
cloudflared tunnel --protocol http2 --url http://localhost:8080
```
* Cloudflare show a link:
```
https://something.trycloudflare.com
```
* Open that URL from browser and the page will work.