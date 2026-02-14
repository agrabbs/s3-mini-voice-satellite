# S3 Mini Voice Satellite

A DIY Home Assistant Voice Assist Satellite using the Wemos ESP32-S3 Mini board. Talk to your smart home with wake word detection, on-device voice processing, and LED feedback.

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![ESPHome](https://img.shields.io/badge/ESPHome-2024.11+-green.svg)
![Home Assistant](https://img.shields.io/badge/Home%20Assistant-2023.10+-blue.svg)

## Features

- 🎤 **Voice Control**: High-quality I2S microphone with noise suppression
- 🔊 **Clear Audio**: I2S amplifier with 3W speaker output
- 🌟 **Wake Word Detection**: On-device processing with multiple wake word support
- 💡 **LED Feedback**: RGB LED status indicator for different states
- 📢 **Media Player**: Play announcements, music, and audio from Home Assistant
- 🔔 **Announcement Chime**: Configurable chime before announcements
- 🔄 **OTA Updates**: Wireless firmware updates via Home Assistant
- 🌐 **Easy Installation**: Web-based installer - no coding required
- 🏠 **Native HA Integration**: Seamless integration with Home Assistant Assist
- 🎯 **Assist Satellite**: Full support for assist_satellite.announce actions

## Hardware Requirements

### Bill of Materials (BOM)

| Component | Description | Approximate Cost | Purchase Link |
|-----------|-------------|------------------|---------------|
| ESP32-S3 Mini | Wemos S3 Mini Development Board | $5-7 | [AliExpress](https://www.wemos.cc/en/latest/s3/s3_mini.html) |
| INMP441 | I2S MEMS Microphone Module | $2-3 | [Amazon](https://www.amazon.com/s?k=INMP441) / AliExpress |
| MAX98357A | I2S 3W Class D Amplifier | $4-6 | [Adafruit](https://www.adafruit.com/product/3006) |
| Speaker | 3W 4Ω GIKFUN Speaker | $3-5 | [Amazon](https://www.amazon.com/Gikfun-Speaker-Stereo-Loudspeaker-Arduino/dp/B01CHYIU26) |
| USB-C Cable | For power and programming | $2-3 | Any electronics store |
| Jumper Wires | Female-to-female dupont wires | $2-3 | Any electronics store |
| **Total** | | **$18-27** | |

### Optional Components

- Custom enclosure (STL files available in `/enclosure` directory - coming soon)
- External RGB LED (if not using built-in LED)
- Push button for manual activation (future feature)

## Pin Connections

### I2S Microphone (INMP441)

| INMP441 Pin | ESP32-S3 Pin | Description |
|-------------|--------------|-------------|
| SCK (Clock) | GPIO7 | I2S Serial Clock |
| WS (Select) | GPIO8 | I2S Word Select |
| SD (Data) | GPIO9 | Serial Data Out |
| VDD | 3.3V | Power |
| GND | GND | Ground |
| L/R | GND | Left channel (tie to GND) |

### I2S Amplifier (MAX98357A)

| MAX98357A Pin | ESP32-S3 Pin | Description |
|---------------|--------------|-------------|
| BCLK | GPIO4 | I2S Bit Clock |
| LRC (LRCLK) | GPIO5 | I2S Left/Right Clock |
| DIN | GPIO6 | I2S Data Input |
| VIN | 5V (USB) | Power (can use 3.3V or 5V) |
| GND | GND | Ground |
| SD | Not connected* | Shutdown control (optional) |

*Leave SD floating or tie to VIN for always-on operation

### Speaker Connection

Connect the 3W speaker to the MAX98357A amplifier terminals:
- Speaker (+) → MAX98357A (+)
- Speaker (-) → MAX98357A (-)

### Status LED

The built-in RGB LED on GPIO47 is used for status indication. No additional wiring needed.

**LED Status Indicators:**
- 🔵 **Blue Pulse**: Listening for voice command
- 🔷 **Cyan Pulse**: Processing your request
- 🟢 **Green Pulse**: Speaking response
- 🟢 **Green Flash**: Connected to Home Assistant
- 🔴 **Red Flash**: Error occurred
- 🔴 **Red Solid**: Disconnected from Home Assistant

## Software Prerequisites

- **Home Assistant** 2023.10 or later with Home Assistant Operating System
- **Home Assistant Cloud** subscription OR locally configured [Assist Pipeline](https://www.home-assistant.io/voice_control/voice_remote_local_assistant/)
- Wake word add-on enabled in Home Assistant
- Chrome or Chromium-based browser (Edge, Brave, etc.) for web installation

## Installation

### Option 1: Web Installer (Recommended)

This is the easiest method for most users.

1. **Visit the Web Installer**
   - Go to: [https://agrabbs.github.io/s3-mini-voice-satellite/](about:blank) *(update this link)*
   - Make sure you're using a Chromium-based browser on desktop (not mobile)

2. **Connect Your Device**
   - Click the **"Connect"** button
   - Plug in your ESP32-S3 Mini via USB-C
   - Select the COM/Serial port that appears
   - Click **"Connect"**

3. **Install Firmware**
   - Click **"Install Voice Assistant"**
   - Wait for the installation to complete (2-5 minutes)

4. **Configure WiFi**
   - Enter your 2.4GHz WiFi credentials
   - Click **"Connect"**

5. **Add to Home Assistant**
   - Click **"Add to Home Assistant"**
   - Follow the setup wizard in Home Assistant
   - Configure wake word and voice preferences

### Option 2: ESPHome Dashboard

For advanced users who want to customize the configuration.

1. **Copy Configuration Files**
   ```bash
   git clone https://github.com/agrabbs/s3-mini-voice-satellite
   cd s3-mini-voice-satellite
   cp secrets.yaml.example secrets.yaml
   ```

2. **Edit secrets.yaml**
   ```yaml
   wifi_ssid: "YourWiFiSSID"
   wifi_password: "YourWiFiPassword"
   api_encryption_key: "generate-using-esphome"
   ota_password: "your-secure-password"
   ap_password: "fallback-password"
   ```

3. **Install ESPHome**
   ```bash
   pip install esphome
   ```

4. **Compile and Upload**
   ```bash
   esphome run s3-mini-voice-satellite.yaml
   ```

5. **Add to Home Assistant**
   - Go to Settings → Devices & Services
   - ESPHome integration should auto-discover your device
   - Click "Configure" and enter your API encryption key

## Home Assistant Configuration

### 1. Enable Voice Assistant

1. Go to **Settings** → **Voice Assistants**
2. Click **"Add Assistant"**
3. Configure your preferred:
   - Speech-to-Text (STT) service
   - Text-to-Speech (TTS) service
   - Conversation agent

### 2. Install Wake Word Add-on

1. Go to **Settings** → **Add-ons**
2. Search for "Piper" or your preferred wake word engine
3. Install and start the add-on
4. Configure your wake word (e.g., "Hey Jarvis", "OK Nabu")

### 3. Configure the Satellite

1. Go to **Settings** → **Devices & Services** → **ESPHome**
2. Find your "S3 Mini Voice Satellite"
3. Click on the device
4. Configure:
   - **Use Wake Word**: Enable/disable wake word detection
   - **Pipeline**: Select your voice assistant pipeline
   - **Wake Word Model**: Choose from available models

### 4. Adjust Silence Detection (Optional)

If the voice assistant takes too long to process your command:

1. Go to the device configuration
2. Under **Configuration**, change **"Finished speaking detection"**
3. Lower values (e.g., 0.5s) = faster response but may cut off speech
4. Higher values (e.g., 2s) = won't cut off but slower response

## Usage

### With Wake Word

1. Say your wake word (e.g., "Hey Jarvis")
2. Wait for the blue LED to start pulsing
3. Speak your command (e.g., "Turn off the living room lights")
4. The LED will turn cyan while processing
5. The LED will turn green when responding
6. The device returns to listening mode automatically

### Manual Activation (Without Wake Word)

1. In Home Assistant, find your S3 Mini Voice Satellite device
2. Press the **"Start Voice Assistant"** button
3. Speak your command within 5 seconds
4. The device will process and respond

### Common Voice Commands

- "Turn on/off the [room] lights"
- "Set the [room] temperature to [X] degrees"
- "What's the temperature in the [room]?"
- "Is the [device] on?"
- "Open/close the [cover]"

For more commands, see [Home Assistant Built-in Sentences](https://www.home-assistant.io/voice_control/builtin_sentences/)

## Troubleshooting

### Device Won't Connect to WiFi

1. Check that you're using a 2.4GHz network (5GHz not supported)
2. Verify WiFi credentials in `secrets.yaml`
3. Look for the fallback AP: "S3 Mini Voice Satellite Fallback"
4. Connect to it and reconfigure WiFi

### No Audio from Speaker

1. Check all MAX98357A pin connections
2. Verify speaker is connected with correct polarity
3. Check if SD pin on MAX98357A is floating (not grounded)
4. Test with higher volume_multiplier in configuration
5. Verify 5V power is connected to MAX98357A VIN

### Microphone Not Working

1. Verify all INMP441 connections
2. Check that L/R pin is grounded (for left channel)
3. Ensure 3.3V power is stable
4. Test with higher auto_gain setting
5. Check ESPHome logs for I2S errors

### Wake Word Not Detected

1. Verify wake word add-on is running in Home Assistant
2. Check that "Use Wake Word" switch is ON
3. Try speaking louder and more clearly
4. Reduce background noise
5. Position microphone away from speaker to avoid feedback
6. Try a different wake word model

### LED Not Working

1. Built-in LED should work automatically on GPIO47
2. Check ESPHome logs for WS2812 errors
3. If using external LED, verify data pin connection
4. Some S3 Mini clones may have LED on different GPIO

### Device Keeps Restarting

1. Check power supply - use 5V 2A minimum
2. Reduce volume_multiplier if speaker draws too much current
3. Check ESPHome logs for memory errors
4. Disable web_server component to save memory
5. May need to add external capacitor to 5V line

### Voice Assistant Not Responding

1. Check Home Assistant connection status
2. Verify API encryption key matches
3. Check that voice assistant pipeline is configured
4. Review Home Assistant logs for errors
5. Test the pipeline in HA's Voice Assistant settings

### Poor Audio Quality

1. Adjust noise_suppression_level (try 1-4)
2. Adjust auto_gain setting (try 15-31 dBFS)
3. Change volume_multiplier (try 1.5-3.0)
4. Check for electrical interference near microphone
5. Position speaker away from microphone

## Advanced Configuration

### Changing Wake Words

The device supports multiple wake word models. To change:

1. In Home Assistant, go to your device settings
2. Find the wake word configuration
3. Select from available models:
   - hey_jarvis
   - ok_nabu
   - alexa
   - hey_mycroft
   - (others available via HA add-ons)

### Custom Pin Configuration

Edit `s3-mini-voice-satellite.yaml` substitutions section:

```yaml
substitutions:
  # Change these pins if needed
  mic_i2s_sck: GPIO7
  mic_i2s_ws: GPIO8
  mic_i2s_sd: GPIO9
  speaker_i2s_bclk: GPIO4
  speaker_i2s_lrc: GPIO5
  speaker_i2s_din: GPIO6
  led_pin: GPIO47
```

### Audio Quality Tuning

```yaml
voice_assistant:
  noise_suppression_level: 2      # 0-4, higher = more suppression
  auto_gain: 31dBFS               # 0-31, higher = more gain
  volume_multiplier: 2.0          # Adjust speaker volume
```

### Adding a Push Button

Add to your YAML configuration:

```yaml
binary_sensor:
  - platform: gpio
    pin:
      number: GPIO1  # Change to your button pin
      mode: INPUT_PULLUP
      inverted: true
    name: "Push to Talk"
    on_press:
      - if:
          condition: voice_assistant.is_running
          then:
            - voice_assistant.stop:
          else:
            - voice_assistant.start:
```

## Power Consumption

Typical power usage:
- **Idle (wake word listening)**: ~150-200mA @ 5V
- **Processing voice**: ~250-350mA @ 5V
- **Playing audio**: ~300-500mA @ 5V (depends on volume)

**Recommended power supply**: 5V 2A USB power adapter

## Troubleshooting

### Audio Noise / Fuzz from Speaker

**Symptoms:** Static, hiss, or crackling from speaker

**Solutions:**
1. **Add 220µF capacitor** between MAX98357A VIN and GND pins (most effective)
2. **Connect GAIN pin to GND** (reduces gain from 15dB to 12dB)
3. **Use dedicated 5V power supply** (2A minimum, avoid computer USB)
4. **Lower volume_multiplier** in YAML (try 0.3 or 0.1)
5. **Twist speaker wires** to reduce EMI

### assist_satellite.announce Not Working

**Check:**
- Home Assistant 2024.10+ required
- Device should show in Settings → Voice Assistants → Devices
- Use entity: `assist_satellite.s3_mini_voice_satellite_XXXXXX`

### Media Player Volume Not Working

**Ensure:** Firmware flashed after adding media_player component. Volume controlled via `media_player` entity, not voice_assistant.

### Device Won't Connect to WiFi

- Ensure 2.4GHz network (5GHz not supported)
- Check credentials in `secrets.yaml`
- Device creates fallback AP: "S3 Mini Voice Satellite Fallback"

## Updating Firmware

### Over-the-Air (OTA) Updates

1. Make changes to `s3-mini-voice-satellite.yaml`
2. Run: `esphome run s3-mini-voice-satellite.yaml`
3. Select "Over the Air" option
4. Wait for upload to complete

### Via Home Assistant

1. Go to **Settings** → **Add-ons**
2. Install **ESPHome** add-on
3. Add your YAML configuration
4. Click **Install** → **Wirelessly**

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## Community & Support

- **Issues**: [GitHub Issues](https://github.com/YOUR-USERNAME/S3-Mini-Voice-Satellite/issues)
- **Discussions**: [GitHub Discussions](https://github.com/YOUR-USERNAME/S3-Mini-Voice-Satellite/discussions)
- **Home Assistant Community**: [Voice Assistant Category](https://community.home-assistant.io/c/voice-assistant/)

## Roadmap

- [ ] Add push-to-talk button support
- [ ] Hardware mute switch option
- [ ] Volume control buttons
- [ ] OLED display integration
- [ ] Battery-powered version
- [ ] 3D printable enclosure designs
- [ ] Multi-room audio synchronization
- [ ] Custom wake word training guide

## Credits

- **ESPHome**: [esphome.io](https://esphome.io/)
- **Home Assistant**: [home-assistant.io](https://www.home-assistant.io/)
- Inspired by the [M5Stack ATOM Echo](https://github.com/esphome/wake-word-voice-assistants) project
- Hardware inspiration from the Home Assistant community

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Disclaimer

This is a DIY project. While tested, no guarantees are made regarding functionality or safety. Build and use at your own risk. Always use appropriate power supplies and follow electrical safety guidelines.

---

**Made with ❤️ for the Home Assistant community**

*If this project helped you, consider giving it a ⭐ on GitHub!*
