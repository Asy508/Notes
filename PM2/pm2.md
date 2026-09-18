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
or cloudflared with custom config file
```bash
pm2 start /usr/local/bin/cloudflared --name custom-tunnel -- \
  tunnel --config /home/armscloud/.cloudflared/custom-config.yml run custom-tunnel
  
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

# Example 5: Laravel Queue Worker

This guide outlines the production architecture and maintenance commands for offl

---

## 1. Architectural Strategy
* **The Problem (Before):** Synchronous file parsing causes requests to hang (400ms - 30,000ms+). This triggers **HTTP 500 (Timeouts)** on the server and **HTTP 429 (Too Many Requests)** on Cloudflare due to client retry loops.
* **The Solution (After):** The client uploads the file payload, the controller saves it to a temporary path, dispatches an asynchronous job row to the database checklist, and immediately returns an **HTTP 200 OK** in **~3.6 ms**. The active network socket closes instantly, and **PM2** handles the processing silently in the background.

---

## 2. Infrastructure Prerequisites

### Environment Configuration (`.env`)
Instruct Laravel to utilize your database as the temporary holding area map instead of executing synchronously:
```env
QUEUE_CONNECTION=database
```

### Database Migration
Generate the necessary system schema tables required by Laravel to track pending background tasks:
```bash
php artisan queue:table
php artisan migrate
```

---

## 3. Core PM2 Control Commands

Manage your Laravel queue daemon using the same commands alongside your Python/Node applications:

### Start the Worker Daemon
Launches the long-running worker stream engine under your active user profile:
```bash
pm2 start "php /var/www/support/artisan queue:work --sleep=3 --tries=3" --name "laravel-worker"
```

### Persist Across System Reboots
Saves the running dashboard process registry list so it restarts automatically if the operating system reboots:
```bash
pm2 save
```

### Live Status Monitoring
View process states, memory footprints, and resource usage:
```bash
pm2 list
# Or use the visual terminal metrics dashboard:
pm2 monit
```

### Tail Live Process Logs
Track backend queue durations, print success outputs, or catch syntax runtime failures instantly:
```bash
pm2 logs laravel-worker
```

---

## 4. Operational Maintenance & Golden Rules

### ⚠️ Golden Rule 1: Restart After Every Deployment
Because PM2 keeps your Laravel job logic permanently loaded into the server's RAM for optimal execution speed, **you must restart the PM2 worker whenever you update or modify your background Job files** (`.php`). If you skip this, the worker will run old cached code:
```bash
pm2 restart laravel-worker
```

---

## 5. Housekeeping & Troubleshooting

### Flush Failed Test Backlogs
If jobs fail during staging (e.g., due to file locking issues), flush out the database history queue logs to start with a clean tracker:
```bash
php artisan queue:flush
```

### Scaling Up (Adding More Queues)
By default, **you do not need another worker process** for new distinct job scripts (e.g., sending notification alerts, report updates). Laravel handles multiple classes under the same generic database list loop. Your existing worker will execute them sequentially automatically.

## 6. How to Create and Dispatch a Job (Template: MyJob)

### Step 1: Generate the Job Class via Terminal
Run the following artisan command inside your project directory to create a fresh, clean job skeleton file:
```bash
php artisan make:job MyJob
```
*This automatically generates a new file located at `app/Jobs/MyJob.php`.*

### Step 2: Write the Job Logic (`app/Jobs/MyJob.php`)
Open the generated file. Pass any variables your task needs (like a user ID, email address, or text string) into the constructor, and write the heavy background task inside the `handle()` method:

```php
namespace App\Jobs;

use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Foundation\Bus\Dispatchable;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Queue\SerializesModels;
use Illuminate\Support\Facades\Log;

class MyJob implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

    // Define properties to hold data passed to the job
    protected \$inputData;

    /**
     * 1. The Constructor: Accept data variables when the job is born.
     */
    public function __construct(\$inputData)
    {
        this->inputData = inputData;
    }

    /**
     * 2. The Handle Method: PM2 runs this code silently in the background.
     */
    public function handle(): void
    {
        // Example background work (e.g., calling an external API, sending emails, processing data)
        Log::info("MyJob started processing data: " . \$this->inputData);

        // Your heavy, time-consuming code goes here...
        sleep(5); 

        Log::info("MyJob successfully completed!");
    }
}
```

### Step 3: Dispatch the Job from a Route or Controller (`routes/api.php`)
Import the `MyJob` namespace at the top of your route or controller file, then trigger it asynchronously using the static `dispatch()` method. 

```php
use App\Jobs\MyJob;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Route;

Route::post('/run-generic-task', function (Request \(request) {\)dataToSend = "Sample Payload Data String";

    // This hands off the task to the database checklist queue instantly!
    MyJob::dispatch(\$dataToSend);

    // The server responds to the user right away in ~3ms without waiting for the 5-second sleep
    return response()->json([
        'message' => 'The request was received and queued for background processing.'
    ], 200);
});
```

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