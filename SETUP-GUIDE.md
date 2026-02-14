# S3 Mini Voice Satellite - Setup Guide

This guide will walk you through the complete setup process, from soldering components to configuring Home Assistant.

## Table of Contents

1. [Hardware Assembly](#hardware-assembly)
2. [Firmware Installation](#firmware-installation)
3. [Home Assistant Configuration](#home-assistant-configuration)
4. [Testing & Validation](#testing--validation)
5. [Advanced Configuration](#advanced-configuration)
6. [Troubleshooting](#troubleshooting)

## Hardware Assembly

### What You'll Need

Refer to the [README.md](README.md#hardware-requirements) for the complete Bill of Materials.

### Assembly Steps

#### Step 1: Prepare Your Workspace

- Clean, static-free workspace
- Good lighting
- Soldering iron set to 350°C (660°F)
- Helping hands or PCB holder
- Wire strippers
- Multimeter

#### Step 2: Solder Pin Headers (Optional)

If your ESP32-S3 Mini doesn't have headers:

1. Insert pin headers into breadboard
2. Place ESP32-S3 Mini on top
3. Solder all pins
4. Check for cold solder joints

#### Step 3: Wire Connections

Follow the detailed [WIRING.md](WIRING.md) guide for complete connection instructions.

**Quick Reference:**

```
INMP441 → ESP32-S3 Mini
  VDD → 3.3V (Pin 1 or 2)
  GND → GND (Pin 39 or 40)
  SCK → GPIO7 (Pin 11)
  WS  → GPIO8 (Pin 12)
  SD  → GPIO9 (Pin 13)
  L/R → GND (Pin 39 or 40)

MAX98357A → ESP32-S3 Mini
  VIN  → 5V (USB VBUS)
  GND  → GND (Pin 39 or 40)
  BCLK → GPIO4 (Pin 8)
  LRC  → GPIO5 (Pin 9)
  DIN  → GPIO6 (Pin 10)
  
Speaker → MAX98357A
  (+) → OUT+
  (-) → OUT-
```

#### Step 4: Visual Inspection

Before powering on:

- [ ] All connections are secure
- [ ] No exposed wires touching each other
- [ ] Correct voltage to each component (3.3V for mic, 5V for amp)
- [ ] Solder joints are shiny and smooth
- [ ] Speaker is connected

#### Step 5: Power Test

1. Connect multimeter to measure voltage
2. Plug in USB-C cable (to computer or 5V 2A adapter)
3. Verify 3.3V at INMP441 VDD
4. Verify 5V at MAX98357A VIN
5. Check for any hot components (slight warmth is OK)

## Firmware Installation

### Prerequisites

- [ ] Home Assistant 2023.10 or later installed
- [ ] Chrome, Edge, or Chromium-based browser (desktop)
- [ ] USB-C data cable
- [ ] WiFi network name and password (2.4GHz)

### Method 1: Web Installer (Recommended)

This is the easiest method for most users.

#### 1. Visit the Web Installer

Open your browser and go to:
**https://YOUR-USERNAME.github.io/S3-Mini-Voice-Satellite/**

> ⚠️ Must use Chrome/Edge/Brave on desktop (not mobile)

#### 2. Connect Device

1. Click **"CONNECT & INSTALL"**
2. A popup will appear showing available serial ports
3. Connect your ESP32-S3 Mini via USB-C
4. A new port should appear (e.g., "USB JTAG/serial debug unit")
5. Select the new port
6. Click **"Connect"**

**Troubleshooting:** If no port appears, you may need to install a USB driver:
- Windows: CH343/CH342 driver
- macOS: Usually works without drivers
- Linux: Add user to `dialout` group: `sudo usermod -a -G dialout $USER`

#### 3. Install Firmware

1. Click **"Install"** in the web installer
2. Wait 2-5 minutes for installation
3. Do NOT disconnect during installation
4. You'll see progress: Erasing → Writing → Verifying

#### 4. Configure WiFi

1. When prompted, select your 2.4GHz WiFi network
2. Enter your WiFi password
3. Click **"Connect"**
4. Device will reboot and connect to WiFi

#### 5. Add to Home Assistant

The installer will provide a link to add the device to Home Assistant:

1. Click **"Add to Home Assistant"**
2. If prompted, configure the My Home Assistant URL
3. Click **"Open Link"**
4. Home Assistant will open with a setup dialog

### Method 2: ESPHome Dashboard

For advanced users who want to customize the configuration.

#### 1. Install ESPHome

**Via Home Assistant Add-on:**
1. Go to Settings → Add-ons → Add-on Store
2. Search for "ESPHome"
3. Click "Install"
4. Start the add-on
5. Click "Open Web UI"

**Via Command Line:**
```bash
pip install esphome
```

#### 2. Clone Repository

```bash
git clone https://github.com/YOUR-USERNAME/S3-Mini-Voice-Satellite.git
cd S3-Mini-Voice-Satellite
```

#### 3. Configure Secrets

```bash
cp secrets.yaml.example secrets.yaml
```

Edit `secrets.yaml`:

```yaml
wifi_ssid: "YourWiFiName"
wifi_password: "YourWiFiPassword"
ap_password: "fallback-password-12345"  # For fallback AP
api_encryption_key: ""  # Leave empty, will be generated
ota_password: "your-secure-password"
```

#### 4. Compile and Upload

**First-time flash (USB):**
```bash
esphome run s3-mini-voice-satellite.yaml
```

Select the USB serial port when prompted.

**Subsequent updates (OTA):**
```bash
esphome run s3-mini-voice-satellite.yaml
```

Select "Over The Air" option.

## Home Assistant Configuration

### Step 1: Add ESPHome Integration

If not already added:

1. Go to **Settings** → **Devices & Services**
2. Click **"+ Add Integration"**
3. Search for "ESPHome"
4. Click on "ESPHome"
5. Your device should be auto-discovered

**Manual Addition:**
1. Click **"+ Add Integration"** → **"ESPHome"**
2. Enter device IP address
3. Enter API encryption key from `secrets.yaml`

### Step 2: Configure Voice Assistant Pipeline

#### Option A: Using Home Assistant Cloud (Easiest)

1. Subscribe to [Home Assistant Cloud](https://www.nabucasa.com/)
2. Go to **Settings** → **Voice Assistants**
3. Click **"+ Add Assistant"**
4. Configure:
   - **Name**: "Assist" or custom name
   - **Conversation agent**: Home Assistant
   - **Speech-to-text**: Cloud
   - **Text-to-speech**: Cloud
   - **Wake word**: (will configure in Step 3)
5. Click **"Create"**

#### Option B: Local Pipeline (Advanced)

##### Install Required Add-ons

**Whisper (Speech-to-Text):**
1. Settings → Add-ons → Add-on Store
2. Search "Whisper"
3. Install "Whisper"
4. Configure language and model
5. Start add-on

**Piper (Text-to-Speech):**
1. Settings → Add-ons → Add-on Store
2. Search "Piper"
3. Install "Piper"
4. Configure voice model
5. Start add-on

**Wyoming Protocol:**
Both Whisper and Piper use Wyoming protocol to communicate with Home Assistant.

##### Create Local Pipeline

1. Go to **Settings** → **Voice Assistants**
2. Click **"+ Add Assistant"**
3. Configure:
   - **Name**: "Local Assist"
   - **Conversation agent**: Home Assistant
   - **Speech-to-text**: Whisper
   - **Text-to-speech**: Piper
   - **Wake word**: (will configure next)

### Step 3: Install Wake Word Engine

#### Install openWakeWord Add-on

1. Go to **Settings** → **Add-ons** → **Add-on Store**
2. Search for "openWakeWord"
3. Click **"Install"**
4. In Configuration tab, no changes needed
5. Click **"Start"**
6. Enable "Start on boot"

#### Configure Wake Word

1. Go to **Settings** → **Voice Assistants**
2. Select your voice assistant pipeline
3. Click **"Configure"**
4. Under **Wake word**, click **"Set wake word"**
5. Available options:
   - hey_jarvis
   - ok_nabu
   - alexa
   - hey_mycroft
   - (others available)
6. Select your preferred wake word
7. Click **"Save"**

### Step 4: Configure S3 Mini Device

1. Go to **Settings** → **Devices & Services** → **ESPHome**
2. Click on your **S3 Mini Voice Satellite** device
3. You'll see entities:
   - **Use Wake Word** (switch)
   - **Start Voice Assistant** (button)
   - **Stop Voice Assistant** (button)
   - **Status LED** (light)
   - **WiFi Signal** (sensor)
   - **Uptime** (sensor)
   - Other diagnostic entities

#### Configure Pipeline for Device

1. In the device page, look for **"Configure"** or settings icon
2. Select **Pipeline**: Choose the voice assistant you created
3. Save configuration

#### Enable Wake Word

1. Turn ON the **"Use Wake Word"** switch
2. The device LED should show connection status (green flash)

## Testing & Validation

### Test 1: LED Indicators

The built-in RGB LED should show different colors:

- **Green flash**: Connected to Home Assistant ✓
- **Red solid**: Disconnected from Home Assistant ✗
- **Off**: Idle and ready

### Test 2: Manual Voice Command

1. In Home Assistant, go to your device
2. Click **"Start Voice Assistant"** button
3. LED should turn blue (listening)
4. Say a command: *"What time is it?"*
5. LED turns cyan (processing)
6. LED turns green (speaking)
7. You should hear the response

### Test 3: Wake Word Detection

1. Ensure **"Use Wake Word"** is ON
2. Say your wake word (e.g., "Hey Jarvis")
3. Wait for LED to turn blue (listening mode)
4. Say a command: *"Turn on the living room lights"*
5. Device should process and respond

### Test 4: Voice Commands

Try these commands:

**Information:**
- "What time is it?"
- "What's the weather?"
- "What's the temperature in the bedroom?"

**Device Control:**
- "Turn on the kitchen lights"
- "Turn off all lights"
- "Set bedroom temperature to 72 degrees"
- "Is the garage door open?"

**Areas:**
- "Turn on lights in the living room"
- "What lights are on in the kitchen?"

### Test 5: Audio Quality

**Microphone Test:**
- Speak from 1-3 meters away
- Check if wake word is reliably detected
- Verify commands are understood correctly

**Speaker Test:**
- Response audio should be clear
- No distortion at normal volume
- No popping or crackling sounds

### Test 6: LED Feedback

Verify all LED states:

- **Wake word detected**: Blue flash ✓
- **Listening**: Blue pulse ✓
- **Processing**: Cyan pulse ✓
- **Speaking**: Green pulse ✓
- **Error**: Red flash ✓

## Media Player & Announcements

Your S3 Mini Voice Satellite includes a media player for playing announcements and audio from Home Assistant.

### Using Assist Satellite Announce

Send announcements to your device:

```yaml
action: assist_satellite.announce
target:
  entity_id: assist_satellite.s3_mini_voice_satellite_XXXXXX
data:
  message: "Dinner is ready!"
```

**Without chime:**
```yaml
action: assist_satellite.announce
target:
  entity_id: assist_satellite.s3_mini_voice_satellite_XXXXXX
data:
  message: "Front door is open"
  preannounce: false
```

### Playing Media

Play music or audio streams:

```yaml
action: media_player.play_media
target:
  entity_id: media_player.s3_mini_voice_satellite_XXXXXX
data:
  media_content_id: "http://example.com/audio.mp3"
  media_content_type: "audio/mp3"
```

### Volume Control

Adjust volume via Home Assistant:
- Use the media player volume slider
- Or automation:
```yaml
action: media_player.volume_set
target:
  entity_id: media_player.s3_mini_voice_satellite_XXXXXX
data:
  volume_level: 0.5  # 50%
```

### Announcement Chime Toggle

Enable/disable chime sounds before announcements:
- Entity: `switch.announcement_chime`
- Default: ON
- Toggle in device settings

## Advanced Configuration

### Adjusting Audio Settings

Edit `s3-mini-voice-satellite.yaml`:

```yaml
voice_assistant:
  noise_suppression_level: 2    # 0-4 (try 3 or 4 for noisy environments)
  auto_gain: 31dBFS             # 0-31 (increase if mic too quiet)
  volume_multiplier: 2.0        # 1.0-5.0 (adjust speaker volume)
```

After changes:
```bash
esphome run s3-mini-voice-satellite.yaml
```

### Adjusting Silence Detection

If the device processes commands too quickly/slowly:

1. Go to device in Home Assistant
2. Find **"Finished speaking detection"** setting
3. Adjust value:
   - **Lower** (0.5s-1s): Faster but may cut you off
   - **Higher** (2s-3s): Won't cut off but slower

### Custom LED Colors

Edit LED effects in the YAML:

```yaml
on_listening:
  - light.turn_on:
      id: status_led
      red: 0%      # 0-100%
      green: 0%    # 0-100%
      blue: 100%   # 0-100%
      brightness: 60%
      effect: "Listening Pulse"
```

### Disable Wake Word (Push-to-Talk Only)

1. Turn OFF **"Use Wake Word"** switch in Home Assistant
2. Use **"Start Voice Assistant"** button to activate
3. Or create automation to trigger on button press

### Energy Monitoring

Track power usage:

1. Add template sensor in Home Assistant `configuration.yaml`:

```yaml
template:
  - sensor:
      - name: "Voice Satellite Power"
        unit_of_measurement: "W"
        device_class: power
        state: >
          {% if is_state('binary_sensor.s3_mini_voice_satellite_connected', 'on') %}
            1.0
          {% else %}
            0
          {% endif %}
```

## Troubleshooting

### Device Won't Boot

**LED doesn't light up:**
- Check USB cable is data-capable (not charge-only)
- Try different USB port or power adapter
- Verify 5V at ESP32-S3 USB pin with multimeter
- Check for short circuits

### No Wake Word Detection

**Wake word not recognized:**
- Verify openWakeWord add-on is running
- Check "Use Wake Word" switch is ON
- Speak louder and more clearly
- Reduce background noise
- Move closer to device (1-2 meters)
- Try different wake word model

**LED doesn't turn blue:**
- Check Home Assistant connection
- Verify voice assistant pipeline is configured
- Review ESPHome logs for errors

### Poor Microphone Quality

**Commands not understood:**
- Increase `auto_gain` setting (try 31dBFS)
- Increase `noise_suppression_level` (try 3-4)
- Check INMP441 connections
- Verify L/R pin is grounded
- Keep microphone away from speaker

### Speaker Issues

**No sound from speaker:**
- Verify speaker is connected
- Check MAX98357A has 5V power
- Ensure SD pin is floating (not grounded)
- Increase `volume_multiplier` setting
- Test speaker with multimeter (should be 4Ω)

**Distorted/crackling audio:**
- Lower `volume_multiplier`
- Add 220µF capacitor to MAX98357A VIN
- Use thicker/shorter speaker wires
- Check power supply can provide 2A

### Connectivity Issues

**Device disconnects frequently:**
- Check WiFi signal strength (sensor in HA)
- Move closer to WiFi router
- Reduce WiFi interference
- Use WiFi 2.4GHz (not 5GHz)

**Can't connect to Home Assistant:**
- Verify API encryption key matches
- Check devices are on same network
- Try manually adding device by IP
- Review Home Assistant logs

### LED Not Working

**No LED activity:**
- Built-in LED should be on GPIO47
- Some S3 Mini clones use different pin
- Check board documentation
- Try GPIO48 if GPIO47 doesn't work

## Getting Help

If you're still having issues:

1. **Check Logs:**
   - ESPHome logs: `esphome logs s3-mini-voice-satellite.yaml`
   - Home Assistant logs: Settings → System → Logs

2. **Search Issues:**
   - [GitHub Issues](https://github.com/YOUR-USERNAME/S3-Mini-Voice-Satellite/issues)

3. **Ask for Help:**
   - [GitHub Discussions](https://github.com/YOUR-USERNAME/S3-Mini-Voice-Satellite/discussions)
   - [Home Assistant Community](https://community.home-assistant.io/)

4. **Include Information:**
   - ESPHome version
   - Home Assistant version
   - Hardware details
   - Relevant logs
   - What you've already tried

## Next Steps

### Enhance Your Setup

- Add custom sentences for automation
- Create AI personality for responses
- Set up multi-room audio
- Add more voice satellites
- Configure advanced automations

### Share Your Build

- Post photos in GitHub Discussions
- Share your enclosure design
- Contribute improvements
- Help other users

---

**Congratulations!** Your S3 Mini Voice Satellite is now ready to use. Enjoy talking to your smart home! 🎤✨
