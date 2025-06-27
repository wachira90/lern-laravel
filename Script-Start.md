```bash
#!/bin/bash
LARAVEL_COMMAND="php artisan serve --host=0.0.0.0"
DEFAULT_PORT="8000"
PID_FILE="laravel_server.pid"

start() {
  local port=$DEFAULT_PORT
  if [ -n "$2" ] && [[ "$2" =~ ^[0-9]+$ ]]; then
    port="$2"
  fi

  if [ -f "$PID_FILE" ]; then
    echo "Laravel server is already running (PID: $(cat "$PID_FILE")) on port $port."
    exit 1
  fi

  echo "Starting Laravel server on port $port..."
  $LARAVEL_COMMAND --port="$port" > laravel.log 2>&1 &
  echo $! > "$PID_FILE"
  echo "Laravel server started in background. PID: $(cat "$PID_FILE") on port $port. See laravel.log for details."
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
    # Try to extract the port from the command line (may not always be accurate)
    PORT=$(ps -p "$PID" -o command= | grep "artisan serve" | grep -oE '--port=[0-9]+' | cut -d'=' -f2)
    if [ -n "$PORT" ]; then
      echo "Laravel server is running (PID: $PID) on port $PORT."
    else
      echo "Laravel server is running (PID: $PID)."
    fi
  else
    echo "Laravel server is not running (PID file found, but process not active)."
    rm "$PID_FILE"
  fi
}

restart() {
  local port_arg=""
  if [ "$1" == "restart" ] && [ -n "$2" ] && [[ "$2" =~ ^[0-9]+$ ]]; then
    port_arg="$2"
  fi
  echo "Restarting Laravel server..."
  stop
  sleep 2
  start "$port_arg"
}

case "$1" in
  start)
    start "$2"
    ;;
  stop)
    stop
    ;;
  status)
    status
    ;;
  restart)
    restart "$2"
    ;;
  *)
    echo "Usage: $0 {start [port]|stop|status|restart [port]}"
    echo "   - port: Optional port number to use with the start or restart command."
    exit 1
    ;;
esac

exit 0
```

**การใช้งาน:**

1.  **Default Port:** กำหนดตัวแปร `DEFAULT_PORT` เป็น `8000` เพื่อเก็บค่า port เริ่มต้น
2.  **แก้ไขฟังก์ชัน `start()`:**
      * ฟังก์ชัน `start()` ตอนนี้รับ Argument ที่ 2 (`$2`) ซึ่งคาดว่าเป็นหมายเลข port
      * ตรวจสอบว่า Argument ที่ 2 มีค่าและเป็นตัวเลขหรือไม่ ถ้าใช่ จะใช้ค่า Argument นั้นเป็น port
      * ใช้ `--port="$port"` ในคำสั่ง `$LARAVEL_COMMAND` เพื่อกำหนด port
      * แสดงหมายเลข port ใน message ต่างๆ
3.  **แก้ไข `case` statement:**
      * เมื่อเรียก script ด้วย `start`, จะส่ง Argument ที่ 2 (`$2`) ไปยังฟังก์ชัน `start()`
4.  **แก้ไขฟังก์ชัน `restart()`:**
      * ฟังก์ชัน `restart()` ก็รับ Argument ที่ 2 (`$2`) เป็น port number เช่นกัน และส่งต่อไปยังฟังก์ชัน `start`.
5.  **แก้ไขฟังก์ชัน `status()`:**
      * พยายามดึงหมายเลข port จาก command line ของ process ที่กำลังทำงานอยู่ และแสดงผลด้วย
6.  **แก้ไข Usage Message:**
      * ปรับปรุง Usage message ให้ระบุว่าคำสั่ง `start` และ `restart` สามารถมี Argument เป็นหมายเลข port ได้

**วิธีใช้งาน:**

  * **Start ด้วย port เริ่มต้น (8000):**
    ```bash
    ./script_laravel.sh start
    ```
  * **Start ด้วย port ที่ต้องการ (เช่น 8080):**
    ```bash
    ./script_laravel.sh start 8080
    ```
  * **Restart ด้วย port เริ่มต้น:**
    ```bash
    ./script_laravel.sh restart
    ```
  * **Restart ด้วย port ที่ต้องการ:**
    ```bash
    ./script_laravel.sh restart 8080
    ```
  * **Stop:**
    ```bash
    ./script_laravel.sh stop
    ```
  * **Status:**
    ```bash
    ./script_laravel.sh status
    ```

ตอนนี้คุณสามารถระบุหมายเลข port ที่ต้องการเมื่อเริ่มหรือ restart Laravel development server ได้แล้วครับ
