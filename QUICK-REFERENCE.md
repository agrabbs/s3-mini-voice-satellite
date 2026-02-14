# S3 Mini Voice Satellite - Quick Reference Card

## Pin Connections Quick Reference

```
┌─────────────────────────────────────────────────────────┐
│              ESP32-S3 MINI CONNECTIONS                  │
├─────────────────────────────────────────────────────────┤
│ INMP441 Microphone:                                     │
│   VDD → 3.3V (Pin 1/2)    SCK → GPIO7 (Pin 11)         │
│   GND → GND (Pin 39/40)   WS  → GPIO8 (Pin 12)         │
│   L/R → GND (Pin 39/40)   SD  → GPIO9 (Pin 13)         │
├─────────────────────────────────────────────────────────┤
│ MAX98357A Amplifier:                                    │
│   VIN  → 5V (USB)         BCLK → GPIO4 (Pin 8)         │
│   GND  → GND (Pin 39/40)  LRC  → GPIO5 (Pin 9)         │
│   GAIN → Floating         DIN  → GPIO6 (Pin 10)        │
│   SD   → Floating                                       │
├─────────────────────────────────────────────────────────┤
│ Speaker: Connect to MAX98357A OUT+ and OUT-            │
│ LED: Built-in on GPIO47 (no wiring needed)             │
└─────────────────────────────────────────────────────────┘
```

## LED Status Colors

| Color | Pattern | Meaning |
|-------|---------|---------|
| 🟢 Green | Flash | Connected to HA |
| 🔵 Blue | Pulse | Listening for command |
| 🔷 Cyan | Pulse | Processing request |
| 🟢 Green | Pulse | Speaking response |
| 🔴 Red | Flash | Error occurred |
| 🔴 Red | Solid | Disconnected from HA |

## Default Audio Settings

```yaml
noise_suppression_level: 2    # 0-4 (2 = moderate)
auto_gain: 31dBFS             # 0-31 (31 = maximum)
volume_multiplier: 2.0        # Speaker volume
sample_rate: 16000            # 16kHz for both mic/speaker
```

## Common Voice Commands

| Command | Example |
|---------|---------|
| Device Control | "Turn on the living room lights" |
| Status Query | "Is the garage door open?" |
| Temperature | "What's the temperature in the bedroom?" |
| Time/Weather | "What time is it?" / "What's the weather?" |
| Area Control | "Turn off all lights in the kitchen" |

## Troubleshooting Quick Fixes

| Problem | Quick Fix |
|---------|-----------|
| No wake word | Check "Use Wake Word" switch is ON |
| No sound | Verify speaker connected, MAX98357A has 5V |
| Poor mic quality | Increase auto_gain to 31, noise_suppression to 3 |
| Disconnects | Check WiFi is 2.4GHz, improve signal strength |
| Cuts off speech | Increase "Finished speaking detection" to 2s |
| Distorted audio | Lower volume_multiplier to 1.5, add capacitor |

## Home Assistant Quick Setup

1. **Install Wake Word Add-on**
   - Settings → Add-ons → openWakeWord

2. **Create Voice Pipeline**
   - Settings → Voice Assistants → Add Assistant
   - Select STT, TTS, and wake word

3. **Configure Device**
   - Settings → Devices → S3 Mini Voice Satellite
   - Turn ON "Use Wake Word"
   - Select your pipeline

## Power Requirements

- **Input:** 5V USB-C (2A minimum recommended)
- **Idle:** 150-200mA
- **Active:** 300-500mA
- **Peak:** Up to 2A during loud audio

## Update Firmware

**Over-the-Air (Easiest):**
```bash
esphome run s3-mini-voice-satellite.yaml
```
Select "Over The Air" option

**Web Installer:**
Visit https://YOUR-USERNAME.github.io/S3-Mini-Voice-Satellite/

## Useful Links

- **Full Documentation:** [README.md](README.md)
- **Wiring Guide:** [WIRING.md](WIRING.md)
- **Setup Guide:** [SETUP-GUIDE.md](SETUP-GUIDE.md)
- **GitHub Issues:** https://github.com/YOUR-USERNAME/S3-Mini-Voice-Satellite/issues
- **Home Assistant Docs:** https://www.home-assistant.io/voice_control/

## File Locations

```
s3-mini-voice-satellite.yaml  → Main ESPHome config
secrets.yaml                  → WiFi credentials (create from .example)
web-installer/                → Browser-based installer
docs/cad/                     → Component 3D models
```

## Configuration Locations

**ESPHome Device:**
- Settings → Devices & Services → ESPHome → S3 Mini Voice Satellite

**Voice Pipeline:**
- Settings → Voice Assistants → [Your Assistant]

**Wake Word:**
- Settings → Add-ons → openWakeWord

## Default Credentials

```yaml
Fallback WiFi SSID: "S3 Mini Voice Satellite Fallback"
Fallback Password:  See secrets.yaml (ap_password)
```

## Pin Summary Table

| Function | GPIO | Pin # |
|----------|------|-------|
| Mic SCK | GPIO7 | 11 |
| Mic WS | GPIO8 | 12 |
| Mic SD | GPIO9 | 13 |
| Amp BCLK | GPIO4 | 8 |
| Amp LRC | GPIO5 | 9 |
| Amp DIN | GPIO6 | 10 |
| RGB LED | GPIO47 | 23 |

---

**Print this page for quick reference while building!**
