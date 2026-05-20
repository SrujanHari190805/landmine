# Acoustic Landmine & Buried Object Detection System

> A seismic detection system that uses solenoid-generated ground impulses, an MPU6050 accelerometer array, and TDOA-based localisation to detect and map buried objects — with real-time visualisation on a Streamlit dashboard and wireless LoRa telemetry.

![Status](https://img.shields.io/badge/Status-In%20Development-yellow)
![Platform](https://img.shields.io/badge/Platform-Raspberry%20Pi%20%7C%20Arduino%20%7C%20LoRa-blue)

---

## ⚠️ Disclaimer

This project is purely for humanitarian demining research and academic purposes. It is intended to assist in the detection of buried objects in safe, controlled settings.

---

## 🔍 Concept

Instead of using metal detectors (which miss non-metallic landmines) or ground-penetrating radar (expensive), this system uses **seismic/acoustic signatures**. A solenoid taps the ground at controlled intervals, generating a consistent mechanical impulse. An array of accelerometers picks up how that impulse propagates through the ground. Buried objects (voids, dense materials) create anomalies in the vibration waveforms — detectable through signal processing.

---


## 🏗️ System Architecture

```
┌─────────────────────────────────┐
│         EXCITATION               │
│  Arduino → Solenoid Driver       │
│  → Ground tap at fixed intervals │
└──────────────┬──────────────────┘
               ↓ Ground vibration propagates
┌──────────────────────────────────────┐
│           SENSING ARRAY              │
│  MPU6050 #1   MPU6050 #2             │
│      ●             ●                 │
│              ★  (anomaly)            │
│      ●             ●                 │
│  MPU6050 #3   MPU6050 #4             │
│  (I2C / SPI to Raspberry Pi)         │
└──────────────┬───────────────────────┘
               ↓ Raw 3-axis accelerometer data
┌──────────────────────────────────────┐
│         SIGNAL PROCESSING (Pi)       │
│  ✦ Bandpass filtering (isolate sig) │
│  ✦ Cross-correlation (align signals) │
│  ✦ STFT spectrogram analysis        │
│  ✦ Matched filtering                 │
│  ✦ TDOA localisation → (x, y) pos  │
└──────────────┬───────────────────────┘
               ↓
┌──────────────────────────────────────┐
│         VISUALISATION & TELEMETRY    │
│  Streamlit dashboard:                │
│    - Raw waveforms                   │
│    - Spectrograms                    │
│    - 2D ground anomaly heatmap       │
│  LoRa wireless telemetry             │
│  Multi-node data aggregation         │
└──────────────────────────────────────┘
```

---

## 🔧 Hardware Components

| Component | Purpose |
|---|---|
| Arduino Uno/Nano | Controls solenoid timing and impulse generation |
| Solenoid + Driver Circuit (TIP120 / L298N) | Generates consistent ground tap impulse |
| MPU6050 Accelerometer × 3–4 | Picks up ground vibration response at multiple points |
| Raspberry Pi | Signal processing, TDOA computation, dashboard |
| LoRa Modules (e.g., SX1276) | Wireless telemetry for multi-node setups |
| Power Bank / Battery | Portable field operation |

---

## ⚙️ Signal Processing Pipeline

### 1. Bandpass Filtering
Isolates the frequency band of interest (ground vibration from the solenoid tap) and removes environmental noise.

### 2. Cross-Correlation
Aligns signals across the 4 accelerometers to measure relative time-of-arrival (TOA) differences.

### 3. STFT Spectrogram Analysis
Short-Time Fourier Transform converts time-domain signals to time-frequency representation — anomalies appear as distinct patterns in the spectrogram.

### 4. Matched Filtering
Correlates received signal against a known "clean" template to improve signal-to-noise ratio for buried object detection.

### 5. TDOA Localisation
Time Difference of Arrival across the sensor array gives the (x, y) position of the buried object using hyperbolic positioning.

```
Position estimation: Δt₁₂ = (d₁ - d₂) / v_sound_in_soil
Solve for intersection of hyperbolas → (x, y) coordinates
```

---

## 📊 Dashboard (Streamlit)

The live Streamlit dashboard shows:
- **Raw waveforms** from all 4 accelerometers
- **Spectrograms** for frequency-domain anomaly detection
- **2D ground anomaly heatmap** — colour-coded map of buried object probability
- Multi-node telemetry aggregation when multiple sensor arrays are deployed via LoRa

---

## 🔮 Future Work

- Automated scanning with motorized cart (integrating XY motion platform)
- Machine learning classifier to distinguish mine-like objects from rocks/roots
- 3D subsurface reconstruction from multiple scan passes
- Field-hardened weatherproof enclosure for outdoor deployment

---

## 👤 Author

**Sri Srujan Hari T**
ECE, BMSIT&M | 2025–Present
