# Getting Started with Laravel Development

A step-by-step blueprint to initialize and spin up a new Laravel application.

---

### Step 1: Install Prerequisites
Ensure your local development machine has the following tools installed and updated:
*   **PHP** — Version 8.2 or higher is recommended.
*   **Composer** — The dependency manager for PHP packages.
*   **Node.js & NPM** — Required for compiling frontend assets (Vite/Tailwind).
*   **Database Engine** — MySQL, PostgreSQL, or SQLite.

### Step 2: Create a New Project
Navigate to your active web directory (e.g., `/var/www` or your user home directory) and generate the workspace:
```bash
composer create-project laravel/laravel my-app
```
*Note: Replace `my-app` with your preferred project directory name. Once downloaded, run `cd my-app` to enter the folder.*

### Step 3: Configure Environment Variables
Open the generated **`.env`** file in your code editor and update the critical configuration strings:

#### 1. Core App Settings
```env
APP_NAME="My Application"
APP_URL=http://127.0.0.1:8000
```

#### 2. Database Connection Strings
```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=my_app_db
DB_USERNAME=root
DB_PASSWORD=your_secure_password
```
*(Ensure you manually create an empty database matching your `DB_DATABASE` name inside your SQL manager before moving to the next step).*

### Step 4: Run Initial Database Migrations
Build the default system tables (e.g., user tables, password resets, and session tokens) by running:
```bash
php artisan migrate
```

### Step 5: Initialize Frontend Compiler (Vite & Tailwind)
Install node modules and launch the hot-reloading development server for UI styles:
```bash
npm install
npm run dev
```
*Tip: Keep this terminal window open in the background. It dynamically compiles assets as you modify code.*

### Step 6: Start the PHP Application Server
Open a separate terminal pane inside your project root and spin up the local Artisan development engine:
```bash
php artisan serve
```
*   Your application is now active at **`http://127.0.0.1:8000`**!

---

### Optional Stack Additions

#### Install Livewire v3
If your project is utilizing the TALL stack (Tailwind, Alpine, Laravel, Livewire), hook up Livewire via Composer:
```bash
composer require livewire/livewire
```

#### Fix Local Permissions (Linux Environments Only)
If you run into `Permission denied` stream issues while testing features on your Linux server workspace:
```bash
sudo chown -R www-data:www-data storage bootstrap/cache
sudo chmod -R 775 storage bootstrap/cache
```
