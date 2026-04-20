# 🌱 Smart Irrigation System

> An AI-powered smart irrigation system built on **ESP32** with **Blynk 2.0**, **Firebase**, and a **Python Random Forest** model — monitors soil, light, temperature, and humidity to automate plant watering intelligently.

---

## 📸 Preview

| Blynk Dashboard | Firebase Database | AI Output (Colab) |
|:-:|:-:|:-:|
| ![Blynk](images/blynk.png) | ![Firebase](images/colab_out.png) | ![AI](images/ai_out.png) |

---

## ✨ Features

- 🤖 **AI Decision Engine** — Random Forest classifier predicts irrigation need in real-time
- 📡 **Blynk 2.0 Dashboard** — live sensor gauges + remote pump control
- 🔥 **Firebase Realtime Database** — stores timestamped sensor readings every 30s
- 🌡️ **Multi-sensor fusion** — soil moisture, LDR light, DHT11 temp & humidity
- 💧 **Auto / Manual modes** — toggle via app or physical button
- 🖥️ **I²C LCD display** — shows all readings + pump state locally
- ⚡ **Long-press button** — manual pump override without opening the app

---

## 🛠️ Hardware

| Component | Description |
|-----------|-------------|
| NodeMCU ESP32 | Main microcontroller |
| DHT11 | Temperature & Humidity sensor (Pin 4) |
| Soil Moisture Sensor | Analog soil reading (Pin 34) |
| LDR + Voltage Divider | Light level sensing (Pin 35) |
| 1-Channel Relay (5V) | Controls the water pump (Pin 26) |
| I²C LCD 16×2 | Local display (0x27) |
| Push Button | Mode toggle + manual override (Pin 14) |

### Circuit Diagram
![Proteus Simulation](images/proteus_for_prog.png)

---

## 📱 Blynk Virtual Pins

| Pin | Widget | Description |
|-----|--------|-------------|
| V0 | Toggle | Auto Mode ON/OFF |
| V1 | Toggle | Manual Pump ON/OFF |
| V2 | Gauge | Soil Moisture % |
| V3 | Gauge | Light Level % |
| V4 | Gauge | Air Temperature °C |
| V5 | Gauge | Air Humidity % |
| V10 | Label | AI Status (e.g. جفاف 65%) |
| V11 | Label | AI Prediction (يحتاج ري / لا يحتاج ري) |

---

## 🧠 Auto Mode Logic (ESP32)

```
IF temp > 30°C       → Pump OFF  (too hot to water)
IF light ≥ 40%       → Pump OFF  (full sun, avoid evaporation)
IF soil < 40%        → Pump ON   (dry soil)
IF soil > 75%        → Pump OFF  (already wet)
ELSE                 → Pump OFF  (default safe state)
```

---

## 🤖 AI System (Python / Google Colab)

The AI layer runs independently, polling Firebase every 30 seconds:

1. **Trains** a `RandomForestClassifier` on historical irrigation data (`irrigation_history.csv`)
2. **Fetches** latest sensor snapshot from Firebase
3. **Predicts** whether the plant needs water (binary classification)
4. **Pushes** result back to Blynk (V10, V11)

```python
# Quick start
pip install requests pandas numpy scikit-learn firebase-admin
python ai_code.py
```

> ⚠️ If `irrigation_history.csv` is not found, the model trains on synthetic random data (accuracy ~43%). Provide real labeled data for better results.

---

## 🚀 Getting Started

### 1. Clone the repo
```bash
git clone https://github.com/YOUR_USERNAME/smart-irrigation-system.git
cd smart-irrigation-system
```

### 2. Flash the ESP32
- Open `main_cod.txt` (rename to `main.ino`) in **Arduino IDE**
- Install required libraries:
  - `BlynkSimpleEsp32`
  - `DHT sensor library`
  - `LiquidCrystal_I2C`
  - `FirebaseESP32`
- Update credentials:
```cpp
char ssid[] = "YOUR_WIFI";
char pass[] = "YOUR_PASS";
#define BLYNK_AUTH_TOKEN "YOUR_TOKEN"
#define FIREBASE_HOST    "your-db.firebaseio.com"
#define FIREBASE_AUTH    "YOUR_FIREBASE_SECRET"
```
- Upload to ESP32 ✅

### 3. Run the AI (Google Colab or local)
- Open `ai_code.py`
- Update `BLYNK_TOKEN` and `FIREBASE_URL`
- Run — AI starts polling every 30 seconds

---

## 📁 Project Structure

```
smart-irrigation-system/
├── main_cod.txt          # ESP32 firmware (Arduino)
├── ai_code.py            # Python AI + Firebase integration
├── irrigation_history.csv  # (optional) Training dataset
├── images/
│   ├── blynk.png
│   ├── colab_out.png
│   ├── ai_out.png
│   └── proteus_for_prog.png
└── README.md
```

---

## 📦 Dependencies

### Arduino / ESP32
| Library | Version |
|---------|---------|
| Blynk | ≥ 1.3.0 |
| DHT sensor library | ≥ 1.4.4 |
| LiquidCrystal_I2C | ≥ 1.1.2 |
| FirebaseESP32 | ≥ 4.4.0 |

### Python
```
requests
pandas
numpy
scikit-learn
```

---

## ⚙️ Configuration Reference

| Parameter | Default | Description |
|-----------|---------|-------------|
| `DRY_PERCENT` | 40 | Soil % below which pump turns ON |
| `WET_PERCENT` | 75 | Soil % above which pump turns OFF |
| `MAX_TEMP_WATERING` | 30.0°C | Temp threshold to block watering |
| `MAX_LIGHT_WATERING` | 40% | Light threshold to block watering |
| Firebase push interval | 30s | How often ESP32 logs to Firebase |
| AI poll interval | 30s | How often Python fetches + predicts |

---

## 🔮 Future Improvements

- [ ] Replace synthetic training data with real labeled dataset
- [ ] Add OTA (Over-The-Air) firmware updates
- [ ] MQTT instead of Firebase for lower latency
- [ ] Mobile notifications when soil is critically dry
- [ ] Water flow sensor for volume tracking

---

## 📄 License

This project is licensed under the **MIT License** — see [`LICENSE`](LICENSE) for details.

---

## 👤 Author

**Hamdy**  
Electronics & Electrical Communications Engineering  
Higher Technological Institute (H.T.I.)

---

> ⭐ If you found this project useful, consider giving it a star!
