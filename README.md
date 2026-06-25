# 🏥 Portable IoT Based Health Monitoring System
### ESP32 | MAX30102 | DHT22 | NEO-6M GPS | Blynk IoT | C (ESP-IDF)

---

## 📌 Overview
A portable, real-time, IoT-enabled health monitoring system built using the **ESP32 microcontroller**
and multiple biomedical sensors. The system continuously measures **Heart Rate (BPM)**,
**SpO₂**, **Body Temperature**, **Humidity**, and **GPS Location**, and transmits all data
to the **Blynk IoT cloud** for remote monitoring via smartphone.

An intelligent alert mechanism automatically sends **emergency notifications** (email / app alerts)
with the patient's live GPS location whenever any health parameter crosses a critical threshold.

---

## ⚙️ Technologies Used
| Category | Details |
|---|---|
| Microcontroller | ESP32 (ESP32-WROOM) |
| Heart Rate & SpO₂ Sensor | MAX30102 (I²C) |
| Temperature & Humidity Sensor | DHT22 |
| GPS Module | NEO-6M (UART) |
| IoT Platform | Blynk Cloud |
| Communication | Wi-Fi (built-in ESP32) |
| Language | C (ESP-IDF Framework) |
| IDE | VS Code + ESP-IDF Plugin |

---

## 🔌 Hardware Pin Configuration
| Component | ESP32 Pin | Protocol |
|---|---|---|
| MAX30102 SDA | GPIO 21 | I²C |
| MAX30102 SCL | GPIO 22 | I²C |
| DHT22 Data | GPIO 26 | Single-wire |
| GPS TX | GPIO 16 (RX of ESP32) | UART |
| GPS RX | GPIO 17 (TX of ESP32) | UART |
| Alert LED | GPIO 2 | GPIO Output |

---

## 🚨 Health Alert Thresholds
| Parameter | Warning Condition | Alert Type |
|---|---|---|
| Heart Rate | > 130 BPM (Tachycardia) | Critical Alert |
| Heart Rate | < 50 BPM (Bradycardia) | Critical Alert |
| SpO₂ | < 92% | Critical Alert |
| Temperature | > 38°C (Fever) | Critical Alert |

---

## 📱 Blynk Virtual Pin Mapping
| Virtual Pin | Parameter |
|---|---|
| V0 | Heart Rate (BPM) |
| V1 | SpO₂ (%) |
| V2 | Temperature (°C) |
| V3 | Humidity (%) |
| V9 | Alert / Event Log |

---

## 🤖 How It Works
1. **MAX30102** measures raw PPG waveform via I²C every 10 ms
2. ESP32 stores 300 samples in a circular buffer (3 second window)
3. **Moving average DC removal** + **AC peak detection** extracts BPM
4. **Ratio-of-ratios (R)** method estimates SpO₂ from red/IR AC & DC amplitudes
5. **DHT22** reads temperature & humidity every 2 seconds
6. **NEO-6M GPS** parses NMEA sentences (GPGGA/GNGGA) for live coordinates
7. All data is pushed to **Blynk Cloud** every 6 seconds via HTTP GET
8. If any threshold is crossed, an **alert with GPS map link** is sent via Blynk event log

---

## 🧠 Signal Processing Pipeline
```
Raw MAX30102 FIFO Samples (Red + IR)
        |
        v
Circular Buffer (300 samples @ 10ms = 3 sec window)
        |
        v
Moving Average DC Removal (window = 5)
        |
        v
AC Component Extraction
        |
        +------------------+------------------+
        |                                     |
        v                                     v
Peak Detection (BPM)              Ratio-of-Ratios (SpO2)
  - Mean + Std threshold            R = (red_AC/red_DC) /
  - Min peak distance 350ms              (ir_AC/ir_DC)
  - Valid range: 30-180 BPM         SpO2 = 110 - 25*R
        |                                     |
        v                                     v
        BPM Result                       SpO2 Result
```

---

## 🗂️ Repository Structure
```
portable-health-monitor-esp32/
├── main/
│   └── health_monitor.c       # Full ESP-IDF C Source Code
├── CMakeLists.txt             # ESP-IDF Build Config
└── README.md                  # Project Documentation
```

---

## 🚀 Getting Started

### 1. Clone the Repository
```bash
git clone https://github.com/YourUsername/portable-health-monitor-esp32.git
cd portable-health-monitor-esp32
```

### 2. Install ESP-IDF
Follow the official guide: https://docs.espressif.com/projects/esp-idf/en/latest/esp32/get-started/

### 3. Configure Your Credentials
Open `main/health_monitor.c` and update:
```c
#define WIFI_SSID          "YourWiFiSSID"
#define WIFI_PASSWORD      "YourWiFiPassword"
#define BLYNK_AUTH_TOKEN   "YourBlynkAuthToken"
```

### 4. Build & Flash
```bash
idf.py build
idf.py -p COMx flash monitor
```

### 5. Setup Blynk Dashboard
- Create a new template in Blynk Cloud
- Add 4 Gauge widgets → V0, V1, V2, V3
- Add a Log/Terminal widget → V9 for alerts
- Enable Email Notifications for events

---

## 📊 Results
- MAX30102 accurately measures Heart Rate and SpO₂
- DHT22 provides real-time temperature and humidity
- NEO-6M GPS delivers live coordinates for emergency tracking
- Blynk dashboard updates every 6 seconds
- Email alerts sent automatically with Google Maps location link

---

## 👥 Team Members
| Name | Enrollment No. |
|---|---|
| Gaurav Singh Rana | 12324801 |
| Hemant Kumar | 12324886 |
| Vivek Kumar | 12325500 |
| Shubham Kumar | 12326211 |

**Institution:** Lovely Professional University
**Department:** Electronics & Communication Engineering
**Date:** March 2025
