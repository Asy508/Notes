# WSL + Cloudflare Tunnel Quick Test

## 1. Install/check WSL
* Using PowerShell (Administrator) Install
```
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
```
wsl
```
* Output:
```
root@PC:~#
```
* Now, we're inside Linux

## 3. Create Simple website
* Inside Ubuntu
```
mkdir -p /root/test-web
cd /root/test-web
```
* Create a page
```
echo '<h1>Hello world welcom to WSL CloudTunnel</h1>' > index.html
```
## 4. Start web server
```
python3 -m http.server 8080
```
* Output:
```
Serving HTTP on 0.0.0.0 port 8080
```
* Keep this terminal running
## 5. Test Locally
* Open another PowerShell
```
curl http://localhost:8080
```
* We should see our HTML
## 6. Install Cloudflared
* Open another WSL terminal
```
cd /tmp
```
* Download
```
wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
```
* Install:
```
dpkg -i cloudflared-linux-amd64.deb
```
* Check:
```
cloudflared --version
```

## 7. Start Quick Tunnel
```
cloudflared tunnel --protocol http2 --url http://localhost:8080
```
* Cloudflare show a link:
```
https://something.trycloudflare.com
```
* Open that URL from browser and the page will work.