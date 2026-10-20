# High-Rate Vibration Data Acquisition — Teensy 4.x and ESP32

[![Teensy](https://img.shields.io/badge/MCU-Teensy%204.x-555)](https://www.pjrc.com/teensy/)
[![ESP32](https://img.shields.io/badge/MCU-ESP32%20%7C%20ESP32--S3-E7352C)](https://www.espressif.com/)
[![Rate](https://img.shields.io/badge/sampling-25%20kHz-2EA44F)](#key-features)

Firmware for capturing accelerometer and gyroscope data at **25 kHz** and getting it off the microcontroller without losing samples. It pairs an ST **IIS3DWB** wide-bandwidth vibration accelerometer with an InvenSense **MPU9250** gyroscope, and explores four competing system designs for reading both sensors in step.

Built in 2025.

## Key features

- **25 kHz sampling** — a 40 µs `IntervalTimer` on the Teensy, 2,000-sample gyroscope bias calibration.
- **Compact binary frames at 1.5 Mbaud** instead of text output, so the serial link keeps up.
- **Four architectures compared:**

| # | Design | Sketches |
|---|---|---|
| 1 | One Teensy owns both sensors on two SPI buses | `Teensy_Accel_Gyro.ino`, `Teensy_Accel.ino`, `Teensy_Gyro.ino` |
| 2 | Two ESP32s (one per sensor) stream into a Teensy aggregator | `ESP_ACCEL.ino`, `ESP_GYRO.ino`, `Teensy_Gyro_Accel_Accept_Publish.ino` |
| 3 | Hardware VALID/CTS handshake: the aggregator pulls a sample from each node only when both are ready | `esp_accel_isr.ino`, `esp_gyro_isr.ino`, `teensy_accept_isr.ino` (+ `_no_sync` baselines, `_wip` drafts) |
| 4 | Single ESP32, one sensor per core and per SPI bus (HSPI/VSPI), mutex-protected | `ESP32_Dual_Core_MPU_STM.ino`, `esp_dualcore_setup.ino`, `MPU_ESP32_HSPI.ino` |

ESP32-S3 variants: `ESPS3_Accel.ino`, `ESPS3_Gyro.ino`.

## Side finding

While probing a 4 MHz SPI clock on a breadboard, the clock edges curved like a charging capacitor — parasitic capacitance of the breadboard itself ([LinkedIn write-up](https://www.linkedin.com/feed/update/urn:li:activity:7301931492283162624/)).

## Status

Experimental: no single design was chosen. Next steps were a host-side decoder, sample-rate verification and frequency analysis.

## Credits

`IIS3DWB.cpp/.h` is the IIS3DWB driver © 2020 Tlera Corp, kept with its original licence notice.

## Related

[ESP32 EEG recorder](https://github.com/Curious-Utkarsh/esp32-eeg-recorder) · [Wearable BLE telemetry](https://github.com/Curious-Utkarsh/wearable-ble-telemetry)

---
[Kumar Utkarsh](https://github.com/Curious-Utkarsh) · [LinkedIn](https://www.linkedin.com/in/kumar-utkarsh-124aab227/)
