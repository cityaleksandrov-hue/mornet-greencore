# Руководство по настройке Edge Agent (Raspberry Pi)

**Версия:** 1.0  
**Дата:** 17 октября 2025  
**Компонент:** Edge Agent (SmartHome Node)  
**Платформа:** Raspberry Pi 4 Model B

---

## 📋 Обзор

Edge Agent — это компонент IoT слоя MorNet GreenCore, отвечающий за:
- Сбор телеметрии с IoT устройств (датчики, счётчики, розетки)
- Локальную агрегацию данных
- Передачу данных в центральную систему через MQTT
- Локальное хранение данных (оффлайн режим)

---

## 🎯 Требования

### Аппаратные
- **Raspberry Pi 4 Model B** (рекомендуется 4GB RAM)
- **MicroSD карта** (минимум 16GB, класс 10)
- **Блок питания** 5V 3A USB-C
- **Сетевое подключение** (Ethernet или Wi-Fi)

### Программные
- **Raspberry Pi OS** (Bullseye или новее)
- **Python 3.7+**
- **MQTT Broker** (Mosquitto)

---

## 🚀 Установка

### 1. Подготовка системы

```bash
# Обновление системы
sudo apt update && sudo apt upgrade -y

# Установка необходимых пакетов
sudo apt install -y python3-pip python3-tk mosquitto mosquitto-clients

# Установка Python библиотек
sudo pip3 install paho-mqtt psutil
```

### 2. Создание структуры директорий

```bash
# Создание директорий для логов и отчётов
mkdir -p /home/pi/logs/edge-agent/$(date +%Y/%m)
mkdir -p /home/pi/reports/smarthome-node01/$(date +%Y/%m)
```

### 3. Установка скрипта телеметрии

Создайте файл `/home/pi/rpi_telemetry.py` со следующим содержимым:

```python
import psutil, time, json, os, logging
import paho.mqtt.publish as publish
from datetime import datetime
import tkinter as tk
from threading import Thread
import subprocess
import sqlite3

# Конфигурация
BROKER = "localhost"  # Адрес MQTT брокера
TOPIC = "telemetry/rpi01"  # MQTT топик
NODE_ID = "SmartHome_Node01"  # ID узла

# Директории
LOG_DIR = f"/home/pi/logs/edge-agent/{datetime.now().strftime('%Y/%m')}/"
REPORT_DIR = f"/home/pi/reports/smarthome-node01/{datetime.now().strftime('%Y/%m')}/"
os.makedirs(LOG_DIR, exist_ok=True)
os.makedirs(REPORT_DIR, exist_ok=True)

# Файлы
LOG_FILE = f"{LOG_DIR}rpi_telemetry_{datetime.now().strftime('%Y-%m-%d')}.log"
REPORT_FILE = f"{REPORT_DIR}SmartHome_Node01_{datetime.now().strftime('%Y-%m-%d')}.md"
SUMMARY_FILE = f"{REPORT_DIR}summary_{datetime.now().strftime('%Y-%m-%d')}.json"
LEDGER_DB = '/home/pi/token_ledger.db'

# Настройка логирования
logging.basicConfig(
    filename=LOG_FILE, 
    level=logging.INFO, 
    format='%(asctime)s - %(message)s'
)

# Инициализация локального ledger для оффлайн режима
conn = sqlite3.connect(LEDGER_DB)
c = conn.cursor()
c.execute('''CREATE TABLE IF NOT EXISTS e_tokens 
             (timestamp TEXT, kwh REAL, e_token REAL, gcm REAL)''')
conn.commit()

# Инициализация MD отчёта
def init_report():
    if not os.path.exists(REPORT_FILE):
        with open(REPORT_FILE, 'w') as f:
            f.write(f"# SmartHome Node 01 — Телеметрия {datetime.now().strftime('%d.%m.%Y')}\n\n")
            f.write("**Node ID:** SmartHome_Node01\n")
            f.write("**Устройство:** Raspberry Pi 4 Model B\n")
            f.write("**MQTT топик:** `telemetry/rpi01`\n\n")
            f.write("## Телеметрия\n\n")
            f.write("| Timestamp | CPU % | RAM % | Temp °C | Uptime min | Total kWh | GCM | Heat MJ |\n")
            f.write("|-----------|------:|------:|--------:|-----------:|----------:|----:|--------:|\n")

init_report()

# Сбор и отправка телеметрии
def collect_telemetry():
    start_time = time.time()
    total_kwh = 0
    
    while True:
        now = datetime.now()
        
        # Сбор метрик
        cpu = psutil.cpu_percent(interval=1)
        ram = psutil.virtual_memory().percent
        
        # Температура (vcgencmd или psutil)
        try:
            temp_str = subprocess.getoutput('vcgencmd measure_temp')
            temp = float(temp_str.split('=')[1].split("'")[0])
        except:
            temps = psutil.sensors_temperatures()
            temp = temps.get("cpu-thermal", [{}])[0].get("current", 0) if temps else 0
        
        # Uptime
        uptime_min = round((time.time() - start_time) / 60)
        
        # Расчёт энергопотребления (3.8W idle)
        power_w = 3.8
        uptime_h = uptime_min / 60
        total_kwh = (power_w / 1000) * uptime_h
        
        # Расчёт тепла (90% энергии → тепло)
        total_heat_mj = total_kwh * 3.6 * 0.9
        
        # Формирование данных
        data = {
            "timestamp": now.strftime('%Y-%m-%d %H:%M:%S'),
            "node_id": NODE_ID,
            "cpu_percent": round(cpu, 1),
            "ram_percent": round(ram, 1),
            "temp_c": round(temp, 1),
            "uptime_min": uptime_min,
            "total_kwh": round(total_kwh, 4),
            "gcm_total": round(total_kwh, 4),
            "total_heat_mj": round(total_heat_mj, 3)
        }
        
        # Алерт при перегреве
        if temp > 45:
            logging.warning(f"ALERT: Overheating! Temp = {temp}°C")
            print(f"⚠️ АЛЕРТ: Перегрев RPi! Температура = {temp}°C")
        
        # Запись в MD отчёт
        md_line = f"| {data['timestamp']} | {data['cpu_percent']} | {data['ram_percent']} | {data['temp_c']} | {data['uptime_min']} | {data['total_kwh']} | {data['gcm_total']} | {data['total_heat_mj']} |\n"
        with open(REPORT_FILE, 'a') as f:
            f.write(md_line)
        
        # Отправка в MQTT
        try:
            publish.single(TOPIC, json.dumps(data), hostname=BROKER)
            logging.info(f"Sent: {data}")
        except Exception as e:
            logging.error(f"MQTT error: {e}")
            # Сохранение в локальный ledger при ошибке
            c.execute('INSERT INTO e_tokens VALUES (?, ?, ?, ?)',
                     (data['timestamp'], data['total_kwh'], data['total_kwh'], data['gcm_total']))
            conn.commit()
        
        # Ожидание следующей итерации
        time.sleep(60)

# Запуск
if __name__ == "__main__":
    print(f"🚀 Edge Agent запущен: {NODE_ID}")
    print(f"📊 MQTT: {BROKER}/{TOPIC}")
    print(f"📁 Логи: {LOG_FILE}")
    print(f"📄 Отчёт: {REPORT_FILE}")
    collect_telemetry()
```

### 4. Настройка автозапуска

```bash
# Добавление в crontab
crontab -e

# Добавьте следующую строку:
@reboot sleep 30 && python3 /home/pi/rpi_telemetry.py &
```

### 5. Настройка MQTT

```bash
# Запуск MQTT брокера
sudo systemctl enable mosquitto
sudo systemctl start mosquitto

# Проверка статуса
sudo systemctl status mosquitto
```

---

## 🧪 Тестирование

### Запуск скрипта вручную

```bash
python3 /home/pi/rpi_telemetry.py
```

### Проверка MQTT

В отдельном терминале:

```bash
# Подписка на топик
mosquitto_sub -h localhost -t "telemetry/rpi01"
```

Вы должны видеть JSON сообщения каждую минуту.

### Проверка логов

```bash
# Просмотр логов
tail -f /home/pi/logs/edge-agent/$(date +%Y/%m)/rpi_telemetry_$(date +%Y-%m-%d).log

# Просмотр MD отчёта
cat /home/pi/reports/smarthome-node01/$(date +%Y/%m)/SmartHome_Node01_$(date +%Y-%m-%d).md
```

---

## 📊 Структура данных

### MQTT Payload

```json
{
  "timestamp": "2025-10-17 14:30:00",
  "node_id": "SmartHome_Node01",
  "cpu_percent": 15.2,
  "ram_percent": 23.5,
  "temp_c": 42.3,
  "uptime_min": 1440,
  "total_kwh": 0.091,
  "gcm_total": 0.091,
  "total_heat_mj": 0.294
}
```

### Файлы

- **Логи:** `/home/pi/logs/edge-agent/YYYY/MM/rpi_telemetry_YYYY-MM-DD.log`
- **MD отчёт:** `/home/pi/reports/smarthome-node01/YYYY/MM/SmartHome_Node01_YYYY-MM-DD.md`
- **JSON summary:** `/home/pi/reports/smarthome-node01/YYYY/MM/summary_YYYY-MM-DD.json`
- **Локальный ledger:** `/home/pi/token_ledger.db`

---

## 🔧 Конфигурация

### Изменение MQTT брокера

В файле `rpi_telemetry.py` измените:

```python
BROKER = "your-mqtt-broker.com"  # Адрес вашего MQTT брокера
TOPIC = "your/topic"              # Ваш MQTT топик
```

### Изменение частоты сбора данных

В конце функции `collect_telemetry()`:

```python
time.sleep(60)  # Измените на нужное значение в секундах
```

### Изменение мощности устройства

```python
power_w = 3.8  # Измените на фактическую мощность вашего RPi
```

---

## 🐛 Устранение неполадок

### MQTT не подключается

```bash
# Проверка статуса Mosquitto
sudo systemctl status mosquitto

# Перезапуск
sudo systemctl restart mosquitto

# Проверка портов
netstat -tuln | grep 1883
```

### Скрипт не запускается автоматически

```bash
# Проверка crontab
crontab -l

# Проверка логов cron
grep CRON /var/log/syslog
```

### Температура показывает 0

```bash
# Проверка vcgencmd
vcgencmd measure_temp

# Если не работает, установите
sudo apt install libraspberrypi-bin
```

---

## 📚 Дополнительные ресурсы

- [Полный отчёт о настройке](../reports/2025-10-17_edge-agent-setup-report.md)
- [Документация Edge Agent](../../apps/iot/edge-agent/README.md)
- [Архитектура MorNet GreenCore](../architecture/strategy-c-hybrid-architecture.md)

---

## 📝 Примечания

- Скрипт работает в режиме 24/7
- Данные сохраняются локально даже при отсутствии сети
- При восстановлении связи данные из локального ledger могут быть отправлены в центр
- Рекомендуется настроить ротацию логов для экономии места

---

**Версия:** 1.0  
**Последнее обновление:** 17 октября 2025

