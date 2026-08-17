# PM2 Process Manager

PM2 can be used to keep applications and processes running in the background.

It is commonly used for Node.js applications, but it can also run other executables and scripts such as Python, Cloudflared, etc.

---

## Basic Syntax

```bash
pm2 start <program> --name <process-name> -- <arguments>
```

The `--` separates the PM2 options from the arguments passed to the application.

---

## Install PM2

Install PM2 globally using npm:

```bash
npm install -g pm2
```

Check the version:

```bash
pm2 --version
```

---

# Example 1: Node.js Application

Suppose we have:

```text
app.js
```

Start it:

```bash
pm2 start app.js --name my-app
```

Check the process:

```bash
pm2 status
```

View logs:

```bash
pm2 logs my-app
```

Restart:

```bash
pm2 restart my-app
```

Stop:

```bash
pm2 stop my-app
```

Delete from PM2:

```bash
pm2 delete my-app
```

---

# Example 2: Python Application

Suppose we have:

```text
main.py
```

Start it:

```bash
pm2 start main.py --name python-app --interpreter python3
```

Check:

```bash
pm2 status
```

View logs:

```bash
pm2 logs python-app
```

---

# Example 3: Run an Executable

PM2 can also run an executable directly.

Example:

```bash
pm2 start ./my-program --name my-program
```

If the program requires arguments:

```bash
pm2 start ./my-program --name my-program -- --port 8080 --config config.json
```

The important part is:

```text
-- 
```

Everything after `--` is passed to the program.

---

# Example 4: Cloudflare Tunnel

Cloudflared can be managed by PM2.

Example tunnel:

```bash
cloudflared tunnel --protocol http2 run home-server
```

Start it using PM2:

```bash
pm2 start cloudflared --name cloudflare-tunnel -- tunnel --protocol http2 run home-server
```

Check:

```bash
pm2 status
```

View Cloudflared logs:

```bash
pm2 logs cloudflare-tunnel
```

Restart:

```bash
pm2 restart cloudflare-tunnel
```

Stop:

```bash
pm2 stop cloudflare-tunnel
```

Delete:

```bash
pm2 delete cloudflare-tunnel
```

---

# Understanding `--`

For example:

```bash
pm2 start cloudflared --name cloudflare-tunnel -- tunnel --protocol http2 run home-server
```

Breakdown:

```text
pm2 start
    │
    └── Start a process

cloudflared
    │
    └── Program to run

--name cloudflare-tunnel
    │
    └── PM2 process name

--
    │
    └── Everything after this goes to cloudflared

tunnel --protocol http2 run home-server
    │
    └── Arguments passed to cloudflared
```

Therefore, this:

```bash
pm2 start cloudflared --name cloudflare-tunnel -- tunnel --protocol http2 run home-server
```

is essentially running:

```bash
cloudflared tunnel --protocol http2 run home-server
```

but PM2 manages the process.

---

# PM2 Automatically Restarts the Process

If the process crashes, PM2 can automatically restart it.

Check the process:

```bash
pm2 status
```

Example:

```text
┌────┬────────────────────┬────────┬─────────┐
│ id │ name               │ mode   │ status  │
├────┼────────────────────┼────────┼─────────┤
│ 0  │ cloudflare-tunnel  │ fork   │ online  │
└────┴────────────────────┴────────┴─────────┘
```

---

# Save PM2 Processes

After starting the processes you want PM2 to manage:

```bash
pm2 save
```

This saves the current PM2 process list.

View the saved list:

```bash
pm2 list
```

---

# Useful PM2 Commands

| Command | Purpose |
|---|---|
| `pm2 status` | Show running processes |
| `pm2 list` | Show running processes |
| `pm2 logs` | Show all logs |
| `pm2 logs <name>` | Show specific process logs |
| `pm2 start <program>` | Start a process |
| `pm2 restart <name>` | Restart a process |
| `pm2 stop <name>` | Stop a process |
| `pm2 delete <name>` | Remove process from PM2 |
| `pm2 save` | Save current process list |
| `pm2 monit` | Monitor processes |

---

# Example: Local Server

A possible local server setup:

```text
Linux Mini PC
│
├── Nginx
│
├── WordPress
│
├── Laravel
│
├── MQTT
│
└── PM2
    │
    ├── Cloudflare Tunnel
    ├── Node.js Application
    └── Python Application
```

PM2 can manage applications such as:

```text
Node.js
Python
Cloudflared
Other executables
```

---

# Important

PM2 keeps a process running and can restart it if it crashes.

However, `pm2 save` by itself does **not necessarily make PM2 start automatically after a machine reboot**.

On a real Linux server, configure PM2 startup using:

```bash
pm2 startup
```

PM2 will provide a command that should be executed with the appropriate privileges.

Then save the process list:

```bash
pm2 save
```

For a temporary WSL test environment, this automatic startup configuration is usually not necessary.