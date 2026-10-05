# Download the file
```
curl -Lk 'https://visualstudio.com' --output vscode_cli.tar.gz
```
or
```
wget -O - https://update.code.visualstudio.com/latest/cli-alpine-x64/stable | tar -xzf -
```
# Unpack the package
```
tar -xf vscode_cli.tar.gz
```
# Start the tunnel
```
./code tunnel service install
```
