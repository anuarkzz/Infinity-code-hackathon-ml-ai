# Infinity-code-hackathon-ml-ai
Code for detecting threats for hackathon 

Этот проект создан для хакатона. Он представляет собой прототип системы кибербезопасности, которая анализирует сетевой трафик и выявляет подозрительные активности в **реальном времени** с помощью машинного обучения.

##Функциональность
Анализ сетевого трафика поддержка CSV-файлов, PCAP-захватов и live-режима с сетевого интерфейса.  
ML-анализ. использование IsolationForest для поиска аномалий в потоках данных.  
Автоматические алерты при обнаружении подозрительной активности система генерирует предупреждения.  
Webhook-уведомления отправка JSON-оповещений во внешние системы (например, SIEM).  
Рекомендации: система подсказывает, что делать при обнаружении аномалии (например, проверить нагрузку, ограничить соединение, включить DPI).  

##  Установка
1. Склонируйте репозиторий:
```bash
git clone https://github.com/<ВАШ-ЛОГИН>/infinity-code-hackathon.git
cd infinity-code-hackathon
```

2. Установите зависимости:
```bash
pip install -r requirements.txt
```

3. Для **PCAP и live-режима** нужно установить `tshark`:
```bash
# Ubuntu/Debian
sudo apt install tshark

# MacOS
brew install wireshark
```

##  Использование

### 1. Offline анализ (CSV)
Запустите на подготовленном CSV:
```bash
python detector.py --csv traffic.csv
```
CSV должен содержать поля:
```
ts,src,dst,length,proto,sport,dport,tcp_flags
```
Пример CSV уже есть в репозитории (`traffic.csv`)

### 2. Анализ PCAP-файла
```bash
python detector.py --pcap sample.pcap
```

### 3 Live-режим (реальное подключение)
```bash
sudo python detector.py --iface eth0 --window 10
```
Где:
- `eth0` — имя сетевого интерфейса (проверьте через `ifconfig` или `ip a`).
- `--window 10` — размер окна агрегации в секундах.

### 4 Webhook для алертов
Можно отправлять алерты на внешний сервер:
```bash
python detector.py --csv traffic.csv --webhook http://localhost:5000/alert
```

## Пример вывода
```text
[ALERT] 192.168.0.10 -> 8.8.8.8 score=0.934 reason=high_bytes
{
  "timestamp": "2025-10-02T15:30:00Z",
  "src": "192.168.0.10",
  "dst": "8.8.8.8",
  "pkt_count": 10,
  "byte_sum": 1200000,
  "score": 0.934,
  "reason": "high_bytes",
  "recommendation": "Check payload, limit connection, enable DPI."
