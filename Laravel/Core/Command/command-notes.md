# Laravel Command

## Appendix: Common Laravel Artisan Commands Reference

#### App Development & Maintenance
*   `php artisan serve` — Starts the local PHP development server (usually at `http://127.0.0.1:8000`).
*   `php artisan down` — Puts the application into **Maintenance Mode** (users will see a 503 page).
*   `php artisan up` — Brings the application back online out of maintenance mode.
*   `php artisan tinker` — Opens an interactive REPL shell to test PHP code and interact with your database directly.
*   `php artisan about` — Displays a quick snapshot overview of your application status, PHP version, Laravel version, environment variables, and active caches.

#### Database & Migrations
*   `php artisan migrate` — Runs all outstanding database migrations.
*   `php artisan migrate:rollback` — Rolls back the very last batch of migrations.
*   `php artisan migrate:fresh` — Drops all tables and re-runs all migrations from scratch (**destroys all data**).
*   `php artisan db:seed` — Runs database seeders to populate tables with test or dummy data.
*   `php artisan migrate:fresh --seed` — Wipes the database, re-runs all migrations, and seeds it all in one command.

#### Making & Generating Files (Scaffolding)
*   `php artisan make:model ModelName -m` — Creates a new Eloquent model. Adding the `-m` flag automatically generates its corresponding database **migration** file.
*   `php artisan make:controller ControllerName --resource` — Creates a controller. The `--resource` flag includes default CRUD methods (index, create, store, show, edit, update, destroy).
*   `php artisan make:middleware MiddlewareName` — Creates an HTTP request middleware.
*   `php artisan make:livewire ComponentName` — Creates a new **Livewire v3** component (generates both the Class file and the Blade view file).

#### Optimization & Cache Management
*   `php artisan config:cache` — Combines all configuration files into a single file for faster loading.
*   `php artisan config:clear` — Removes the configuration cache file (use this right after changing your `.env` file).
*   `php artisan route:cache` — Caches routes to speed up routing registration.
*   `php artisan route:clear` — Clears the route cache.
*   `php artisan optimize` — Caches config and routes together.
*   `php artisan optimize:clear` — Clears **all** caches (Framework cache, Route cache, Config cache, and Compiled views) all at once. Excellent for troubleshooting weird glitches.