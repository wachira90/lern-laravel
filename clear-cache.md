# Clear Cache

## 1. Configuration Cache

````
$ php artisan config:clear
Configuration cache cleared!

$ php artisan config:cache
Configuration cache cleared!
Configuration cached successfully!

````

## 2. Route Caching

````
$ php artisan route:clear
Route cache cleared!

$ php artisan route:cache
Route cache cleared!
Routes cached successfully!
````

## 3. Views Caching

````
$ php artisan view:clear
Compiled views cleared!

$ php artisan view:cache
Compiled views cleared!
Blade templates cached successfully!
````

## 4. Events Cache

````
$ php artisan event:clear
Cached events cleared!

$ php artisan event:cache
Cached events cleared!
Events cached successfully!
````

## 5. Application Cache

````
$ php artisan cache:clear
Application cache cleared!
````

## 6. Clearing All Cache

````
$ php artisan optimize:clear
Compiled views cleared!
Application cache cleared!
Route cache cleared!
Configuration cache cleared!
Compiled services and packages files removed!
Caches cleared successfully!
````

## Clearing Composer Cache

````
$ composer dump-autoload

$ composer clear-cache
$ composer clearcache
$ composer cc
````

## Clearing NPM Cache

````
$ npm cache clean --force

````

## Summary
````
$ php artisan list
````


https://dev.to/kenfai/laravel-artisan-cache-commands-explained-41e1

