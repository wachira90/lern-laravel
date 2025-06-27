# lerning laravel

Don't forgot add path env for php check by "php -v"

## install composer 

```
https://getcomposer.org/Composer-Setup.exe
```

## check version

````
C:\Users\admin>composer -V
Composer version 2.5.2 2023-02-04 14:33:22
````

## download code by composer

```sh
composer create-project --prefer-dist laravel/laravel example-app
```

## permission folder 

```sh
chown -R adminpcd:nginx example-app/
cd example-app/
chmod -R 0775 storage/
cd storage/
chmod -R 0750 logs/
chmod -R 0754 framework/views/
chmod -R 0770 framework/sessions/
```

## Dockerfile

```Dockerfile
FROM public.ecr.aws/docker/library/php:7.3.28-fpm
RUN mkdir -p /var/www
WORKDIR /var/www
COPY  ./laravel /var/www/
ENV TZ="Asia/Bangkok"
EXPOSE 8000
# RUN php artisan serve
CMD ["php","artisan","serve","--host=0.0.0.0", "--port=8000"]
# php artisan serve --host=0.0.0.0 --port=8000
```

## control script 

```sh
#!/bin/bash
set -e
LARAVEL_COMMAND="php artisan serve --host=0.0.0.0 --port=8000"
PID_FILE="laravel_server.pid"

start() {
  if [ -f "$PID_FILE" ]; then
    echo "Laravel server is already running (PID: $(cat "$PID_FILE"))."
    exit 1
  fi

  echo "Starting Laravel server..."
  $LARAVEL_COMMAND > laravel.log 2>&1 &
  echo $! > "$PID_FILE"
  echo "Laravel server started in background. PID: $(cat "$PID_FILE"). See laravel.log for details."
}

stop() {
  if [ ! -f "$PID_FILE" ]; then
    echo "Laravel server is not running."
    exit 1
  fi

  PID=$(cat "$PID_FILE")
  echo "Stopping Laravel server (PID: $PID)..."
  kill $PID
  if [ $? -eq 0 ]; then
    rm "$PID_FILE"
    echo "Laravel server stopped."
  else
    echo "Failed to stop Laravel server (PID: $PID)."
  fi
}

status() {
  if [ ! -f "$PID_FILE" ]; then
    echo "Laravel server is not running."
    exit 0
  fi

  PID=$(cat "$PID_FILE")
  if ps -p "$PID" > /dev/null; then
    echo "Laravel server is running (PID: $PID)."
  else
    echo "Laravel server is not running (PID file found, but process not active)."
    rm "$PID_FILE"
  fi
}

restart() {
  echo "Restarting Laravel server..."
  stop
  # Wait a moment to ensure the server has stopped
  sleep 2
  start
}

case "$1" in
  start)
    start
    ;;
  stop)
    stop
    ;;
  status)
    status
    ;;
  restart)
    restart
    ;;
  *)
    echo "Usage: $0 {start|stop|status|restart}"
    exit 1
    ;;
esac

exit 0
```


## install global command

```sh
composer global require laravel/installer
```

## เตรียมไฟล์สำหรับสร้างตาราง ตัวอย่างข้างล่างคือสร้างตารางชื่อ blogs

```sh
php artisan make:migration create_blogs_table
```

## สร้างตาราง สั่งให้ทำการสร้างตารางในฐานข้อมูล

```sh
php artisan migrate --force
```

## สร้างไฟล์สำหรับ seeder เพื่อใช้สร้างข้อมูลลงตาราง

```sh
php artisan make:seeder BlogsTableSeeder
```

## หากใช้คำสั่ง php artisan db:seed แล้ว Error ให้ทำการใช้คำสั่งด้านล่างก่อน
```
composer dump-autoload
```

## สร้างข้อมูลโดยเจาะจงเฉพาะ Class
```
php artisan db:seed --class=BlogsTableSeeder
```

## สร้างข้อมูลโดยรัน DatabaseSeeder Class ที่อยู่ในไฟล์ database\DatabaseSeeder.php

```sh
php artisan db:seed 
```

ดูเพิ่มเติม : https://laravel.com/docs/5.4/migrations#generating-migrations


## สร้าง Model โดยจะสร้าง  database migration ให้ด้วย 

#### (ดูเพิ่มเติม https://laravel.com/docs/5.4/eloquent)

```sh
php artisan make:model Blog -m
```

## สร้าง resource controller 

####  (ดูเพิ่มเติม https://laravel.com/docs/5.4/controllers#resource-controllers)

```sh
php artisan make:controller BlogController --resource
```

## สร้าง middleware
```sh
php artisan make:middleware AuthAdministrator
```
