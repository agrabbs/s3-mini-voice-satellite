# Changelog

All notable changes to the S3 Mini Voice Satellite project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Planned
- Push-to-talk button support
- Hardware mute switch option
- Volume control buttons
- OLED display integration
- Battery-powered version with sleep modes
- 3D printable enclosure designs
- Multiple wake word model support
- Custom wake word training guide

## [1.0.0] - 2026-02-13

### Added
- Initial release of S3 Mini Voice Satellite firmware
- ESP32-S3 Mini board support
- INMP441 I2S microphone integration
- MAX98357A I2S amplifier support
- Built-in RGB LED status indicators
- Wake word detection using micro_wake_word component
- Home Assistant voice assistant integration
- Noise suppression (configurable levels 0-4)
- Auto-gain control (0-31 dBFS)
- Volume multiplier for speaker output
- LED effects for different states:
  - Listening (blue pulse)
  - Processing (cyan pulse)
  - Speaking (green pulse)
  - Wake word detected (blue flash)
  - Error (red flash)
  - Connected/Disconnected status
- OTA update support
- Web server for diagnostics
- Fallback WiFi AP for configuration
- Web-based firmware installer
- GitHub Actions CI/CD pipeline
- Comprehensive documentation:
  - README with full setup guide
  - WIRING guide with detailed pin connections
  - CONTRIBUTING guidelines
  - Web installer page
- Home Assistant auto-discovery
- Configurable wake word via Home Assistant
- Start/Stop voice assistant buttons
- WiFi signal and uptime sensors
- Device diagnostics (IP, MAC, version)
- Restart button

### Technical Details
- ESP-IDF framework with optimized settings
- I2S audio bus configuration
- Microphone: 16kHz, 32-bit, mono (left channel)
- Speaker: 16kHz, mono, 2s timeout
- LED: WS2812B on GPIO47
- Memory optimizations for stable operation
- Voice assistant timeout: 300s (5 minutes)

### Documentation
- Complete hardware BOM with purchase links
- Step-by-step wiring instructions
- Visual connection diagrams
- Troubleshooting guide
- Power consumption specifications
- Installation guides (web installer and manual)
- Home Assistant configuration instructions
- Advanced configuration examples

### Infrastructure
- GitHub Actions workflow for firmware builds
- GitHub Pages deployment for web installer
- Automatic release creation on version tags
- Factory binary generation
- Web installer manifest

## Release Notes

### Version 1.0.0 - Initial Release

This is the first stable release of the S3 Mini Voice Satellite project. The firmware provides a complete Home Assistant voice assistant satellite using affordable, readily available components.

**Key Features:**
- 🎤 High-quality voice capture with I2S MEMS microphone
- 🔊 Clear audio playback through I2S amplifier and speaker
- 🌟 On-device wake word detection
- 💡 Visual feedback with RGB LED
- 🔄 Easy wireless updates via Home Assistant
- 🌐 Browser-based installation (no coding required)

**Hardware Cost:** $18-27 USD for all components

**Tested Configuration:**
- Wemos ESP32-S3 Mini (8MB Flash)
- INMP441 I2S MEMS Microphone
- MAX98357A I2S Amplifier
- 3W 4Ω Speaker
- USB-C power supply (5V 2A)

**Compatible With:**
- Home Assistant 2023.10+
- ESPHome 2024.11.0+
- Home Assistant Cloud (Nabu Casa)
- Local Assist Pipeline with Whisper/Piper

**Known Limitations:**
- Requires 2.4GHz WiFi (5GHz not supported)
- Peak current draw can reach 2A during audio playback
- Wake word models require ~2MB flash storage
- No battery operation in this version
- Single microphone (no beamforming/echo cancellation)

**Future Development:**
See the [Unreleased] section above for planned features.

---

## How to Upgrade

### From Web Installer
The easiest way to upgrade is through Home Assistant:
1. Go to Settings → Add-ons → ESPHome
2. Find your device
3. Click "Update" if available

### From ESPHome
```bash
esphome run s3-mini-voice-satellite.yaml
```
Select "Over The Air" when prompted.

### Manual Upgrade
Download the latest firmware from [Releases](https://github.com/YOUR-USERNAME/S3-Mini-Voice-Satellite/releases) and flash using esptool or the web installer.

---

[Unreleased]: https://github.com/YOUR-USERNAME/S3-Mini-Voice-Satellite/compare/v1.0.0...HEAD
[1.0.0]: https://github.com/YOUR-USERNAME/S3-Mini-Voice-Satellite/releases/tag/v1.0.0
