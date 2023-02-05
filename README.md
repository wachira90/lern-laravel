# lern-laravel
lern-laravel


Don't forgot add path env for php check by "php -v"


## install composer 

````
https://getcomposer.org/Composer-Setup.exe
````

## check version

````
C:\Users\admin>composer -V
Composer version 2.5.2 2023-02-04 14:33:22
````

## download code by composer

````
composer create-project --prefer-dist laravel/laravel example-app
````

## install global command
````
composer global require laravel/installer
````

## เตรียมไฟล์สำหรับสร้างตาราง ตัวอย่างข้างล่างคือสร้างตารางชื่อ blogs
````
php artisan make:migration create_blogs_table
````

## สร้างตาราง สั่งให้ทำการสร้างตารางในฐานข้อมูล
````
php artisan migrate --force
````

## สร้างไฟล์สำหรับ seeder เพื่อใช้สร้างข้อมูลลงตาราง
````
php artisan make:seeder BlogsTableSeeder
````

## หากใช้คำสั่ง php artisan db:seed แล้ว Error ให้ทำการใช้คำสั่งด้านล่างก่อน
````
composer dump-autoload
````

## สร้างข้อมูลโดยเจาะจงเฉพาะ Class
````
php artisan db:seed --class=BlogsTableSeeder
````

## สร้างข้อมูลโดยรัน DatabaseSeeder Class ที่อยู่ในไฟล์ database\DatabaseSeeder.php
````
php artisan db:seed 
````

ดูเพิ่มเติม : https://laravel.com/docs/5.4/migrations#generating-migrations


## สร้าง Model โดยจะสร้าง  database migration ให้ด้วย 
#### (ดูเพิ่มเติม https://laravel.com/docs/5.4/eloquent)
````
php artisan make:model Blog -m
````

## สร้าง resource controller 
####  (ดูเพิ่มเติม https://laravel.com/docs/5.4/controllers#resource-controllers)
````
php artisan make:controller BlogController --resource
````

## สร้าง middleware
````
php artisan make:middleware AuthAdministrator
````
