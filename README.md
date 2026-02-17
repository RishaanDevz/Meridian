# Meridian

> A smart longboard system with haptic navigation, ride analytics, and an open companion app.

**Status: Early concept / WIP — hardware design in progress**

---

## What is Meridian?

Meridian is an open-source smart longboard system. It's a hardware module you mount to any longboard deck that adds haptic turn-by-turn navigation, real-time ride sensing, and a session journal — no screen, no distraction, just your board talking to your feet.

The system has two parts:
- **Meridian Board Unit** — a self-contained module that mounts under the deck. Sensors, haptics, GPS, all in one enclosure.
- **Meridian App** — a companion phone app for setting destinations, reviewing ride sessions, and tracking progression over time.

---

## Why

Existing navigation while skating means glancing at your phone or earphones telling you where to turn. Neither is great when you're moving fast or in traffic. Meridian keeps your eyes up and your hands free — the board tells you where to turn through vibration pulses under your feet.

Left foot pulse → turn left. Right foot pulse → turn right. That's it.

---

## Hardware (WIP)

The board unit is built around an **ESP32-S3** and fits inside a CNC-routed channel in the deck under a G10 fiberglass lid.

### Sensors
| Component | Part | Purpose |
|-----------|------|---------|
| MCU | ESP32-S3 | Main compute, BLE, sensor fusion |
| IMU | ICM-42688-P | Carve detection, orientation, motion |
| GPS | u-blox MAX-M10S | Speed, position, route tracking |
| Pressure (×2) | FSR 406 | Foot presence + weight distribution |
| ADC | ADS1115 | Clean 16-bit pressure readings |

### Haptics
Two **30mm ERM pancake motors** (one per foot zone), driven via MOSFET + PWM from the ESP32. Large diameter chosen specifically for through-shoe transmission — small LRAs don't survive the material stack of shoe sole + grip tape + deck.

### Power
- 1S LiPo (2000–3000mAh) in routed deck channel
- USB-C charging via TP4056
- TPS63020 buck-boost → clean 3.3V rail
- MAX17048 fuel gauge over I2C

### Storage
MicroSD via SPI for raw session logging (IMU, GPS, pressure, timestamps at 100Hz).

---

## Navigation

Meridian uses your phone as the routing brain. The board unit is the GPS sensor and haptic actuator — it stays stateless with respect to navigation.



The board doesn't need to know it's navigating. It just receives , , , or  commands and fires the corresponding motor pattern. This keeps the firmware simple and the system robust — if the phone dies, the board keeps logging, nothing crashes.

---

## Ride Metrics

Every session is logged to the onboard SD card and synced to the app post-ride. The session journal includes:

- **Distance & duration**
- **Top speed & average speed**
- **Carve count** — detected via IMU yaw rate + lateral G threshold
- **Best carve** — deepest carve angle in the session
- **Foot pressure balance** — heel/toe and left/right weight distribution over the session
- **Route heatmap** — GPS trace of your ride
- **Elevation profile** — hills, drops, flat sections

Carve detection uses the ICM-42688-P gyro Z-axis (yaw rate) fused with accelerometer lateral G. A carve event is logged when yaw rate crosses threshold and lateral G confirms intentional lean — not a road bump.

---

## Firmware Architecture (WIP)

FreeRTOS on the ESP32-S3, two cores:



BLE GATT layout:


---

## Companion App (WIP)

Built in Flutter (iOS + Android). Three main flows:

**Pre-ride** — destination search (Mapbox), route preview, connect to board over BLE

**During ride** — runs in background, monitors GPS notify from board, fires haptic commands at waypoints, detects off-route and recalculates

**Post-ride** — session summary card, carve breakdown, route replay, historical progression

---

## Roadmap

- [ ] Board unit PCB v0.1
- [ ] ERM haptic bench testing (through-shoe validation)
- [ ] IMU carve detection algorithm
- [ ] BLE GATT implementation
- [ ] Flutter app scaffold
- [ ] Mapbox routing integration
- [ ] SD logging + BLE session sync
- [ ] Enclosure CNC design
- [ ] Field testing
- [ ] watchOS / WearOS companion (future)

---

## Project Structure (planned)



---

## Contributing

This is an early-stage personal project being developed openly. If you're into longboarding, embedded systems, or haptic interfaces — watch the repo. Contributions, issues, and feedback welcome once the foundation is more solid.

---

## License

Hardware: CERN-OHL-S v2
Software & Firmware: MIT# Meridian 🛹

> A smart longboard system with haptic navigation, ride analytics, and an open companion app.

**Status: Early concept / WIP — hardware design in progress**

---

## What is Meridian?

Meridian is an open-source smart longboard system. It's a hardware module you mount to any longboard deck that adds haptic turn-by-turn navigation, real-time ride sensing, and a session journal — no screen, no distraction, just your board talking to your feet.

The system has two parts:
- **Meridian Board Unit** — a self-contained module that mounts under the deck. Sensors, haptics, GPS, all in one enclosure.
- **Meridian App** — a companion phone app for setting destinations, reviewing ride sessions, and tracking progression over time.

---

## Why

Existing navigation while skating means glancing at your phone or earphones telling you where to turn. Neither is great when you're moving fast or in traffic. Meridian keeps your eyes up and your hands free — the board tells you where to turn through vibration pulses under your feet.

Left foot pulse → turn left. Right foot pulse → turn right. That's it.

---

## Hardware (WIP)

The board unit is built around an **ESP32-S3** and fits inside a CNC-routed channel in the deck under a G10 fiberglass lid.

### Sensors
| Component | Part | Purpose |
|-----------|------|---------|
| MCU | ESP32-S3 | Main compute, BLE, sensor fusion |
| IMU | ICM-42688-P | Carve detection, orientation, motion |
| GPS | u-blox MAX-M10S | Speed, position, route tracking |
| Pressure (×2) | FSR 406 | Foot presence + weight distribution |
| ADC | ADS1115 | Clean 16-bit pressure readings |

### Haptics
Two **30mm ERM pancake motors** (one per foot zone), driven via MOSFET + PWM from the ESP32. Large diameter chosen specifically for through-shoe transmission — small LRAs don't survive the material stack of shoe sole + grip tape + deck.

### Power
- 1S LiPo (2000–3000mAh) in routed deck channel
- USB-C charging via TP4056
- TPS63020 buck-boost → clean 3.3V rail
- MAX17048 fuel gauge over I2C

### Storage
MicroSD via SPI for raw session logging (IMU, GPS, pressure, timestamps at 100Hz).

---

## Navigation

Meridian uses your phone as the routing brain. The board unit is the GPS sensor and haptic actuator — it stays stateless with respect to navigation.



The board doesn't need to know it's navigating. It just receives , , , or  commands and fires the corresponding motor pattern. This keeps the firmware simple and the system robust — if the phone dies, the board keeps logging, nothing crashes.

---

## Ride Metrics

Every session is logged to the onboard SD card and synced to the app post-ride. The session journal includes:

- **Distance & duration**
- **Top speed & average speed**
- **Carve count** — detected via IMU yaw rate + lateral G threshold
- **Best carve** — deepest carve angle in the session
- **Foot pressure balance** — heel/toe and left/right weight distribution over the session
- **Route heatmap** — GPS trace of your ride
- **Elevation profile** — hills, drops, flat sections

Carve detection uses the ICM-42688-P gyro Z-axis (yaw rate) fused with accelerometer lateral G. A carve event is logged when yaw rate crosses threshold and lateral G confirms intentional lean — not a road bump.

---

## Firmware Architecture (WIP)

FreeRTOS on the ESP32-S3, two cores:



BLE GATT layout:


---

## Companion App (WIP)

Built in Flutter (iOS + Android). Three main flows:

**Pre-ride** — destination search (Mapbox), route preview, connect to board over BLE

**During ride** — runs in background, monitors GPS notify from board, fires haptic commands at waypoints, detects off-route and recalculates

**Post-ride** — session summary card, carve breakdown, route replay, historical progression

---

## Roadmap

- [ ] Board unit PCB v0.1
- [ ] ERM haptic bench testing (through-shoe validation)
- [ ] IMU carve detection algorithm
- [ ] BLE GATT implementation
- [ ] Flutter app scaffold
- [ ] Mapbox routing integration
- [ ] SD logging + BLE session sync
- [ ] Enclosure CNC design
- [ ] Field testing
- [ ] watchOS / WearOS companion (future)

---

## Project Structure (planned)



---

## Contributing

This is an early-stage personal project being developed openly. If you're into longboarding, embedded systems, or haptic interfaces — watch the repo. Contributions, issues, and feedback welcome once the foundation is more solid.

---

## License

Hardware: CERN-OHL-S v2
Software & Firmware: MIT
