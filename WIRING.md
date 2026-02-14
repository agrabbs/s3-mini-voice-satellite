# S3 Mini Voice Satellite - Wiring Guide

This guide provides detailed wiring instructions for connecting all components of the S3 Mini Voice Satellite.

## ⚠️ Safety First

- Always disconnect power before making any connections
- Double-check polarity before connecting power
- Use proper wire gauge for current requirements
- Avoid short circuits - they can damage components
- Work in a static-safe environment

## Tools & Materials Needed

- Soldering iron and solder (for permanent connections)
- Wire strippers
- Small Phillips screwdriver (for terminal blocks)
- Multimeter (for testing connections)
- Female-to-female jumper wires (if not soldering)
- Heat shrink tubing or electrical tape
- Helping hands or PCB holder

## Component Overview

### ESP32-S3 Mini Pinout Reference

```
                    ┌─────────────┐
                    │   USB-C     │
                    └─────────────┘
                         ESP32-S3 Mini
          ┌────────────────────────────────┐
    3V3  ─┤ 1                          40 ├─ GND
    3V3  ─┤ 2                          39 ├─ GND  
    RST  ─┤ 3                          38 ├─ GPIO46
   GPIO0 ─┤ 4                          37 ├─ GPIO45
   GPIO1 ─┤ 5                          36 ├─ GPIO44
   GPIO2 ─┤ 6                          35 ├─ GPIO43
   GPIO3 ─┤ 7                          34 ├─ GPIO42
   GPIO4 ─┤ 8    [MAX98357A BCLK]     33 ├─ GPIO41
   GPIO5 ─┤ 9    [MAX98357A LRC]      32 ├─ GPIO40
   GPIO6 ─┤10    [MAX98357A DIN]      31 ├─ GPIO39
   GPIO7 ─┤11    [INMP441 SCK]        30 ├─ GPIO38
   GPIO8 ─┤12    [INMP441 WS]         29 ├─ GPIO37
   GPIO9 ─┤13    [INMP441 SD]         28 ├─ GPIO36
  GPIO10 ─┤14                          27 ├─ GPIO35
  GPIO11 ─┤15                          26 ├─ GPIO34 (NC)
  GPIO12 ─┤16                          25 ├─ GPIO33 (NC)
  GPIO13 ─┤17                          24 ├─ GPIO21
  GPIO14 ─┤18                          23 ├─ GPIO47 [RGB LED]
  GPIO15 ─┤19                          22 ├─ GPIO48
  GPIO16 ─┤20                          21 ├─ GPIO17
          └────────────────────────────────┘
```

*Note: GPIO34 and GPIO33 may not be available on some S3 Mini variants*

## Step-by-Step Wiring Instructions

### Step 1: INMP441 I2S Microphone

The INMP441 is an I2S MEMS microphone that captures high-quality audio.

#### Pin Connections

| INMP441 Pin | ESP32-S3 Pin | Wire Color* | Notes |
|-------------|--------------|-------------|-------|
| **VDD** | **3.3V** | Red | Power (3.3V only, NOT 5V!) |
| **GND** | **GND** | Black | Ground |
| **SCK** | **GPIO7** | Yellow | I2S Clock |
| **WS** | **GPIO8** | Green | Word Select / LRCLK |
| **SD** | **GPIO9** | Blue | Serial Data Out |
| **L/R** | **GND** | Black | Left/Right select (GND = Left) |

*Suggested wire colors - use what you have available*

#### Wiring Diagram - INMP441

```
    ┌─────────────────┐
    │    INMP441      │
    │  I2S Mic Module │
    └─────────────────┘
     │ │ │ │ │ │
     │ │ │ │ │ └─── L/R → GND (ESP32-S3 Pin 39/40)
     │ │ │ │ └───── SD  → GPIO9 (ESP32-S3 Pin 13)
     │ │ │ └─────── WS  → GPIO8 (ESP32-S3 Pin 12)
     │ │ └───────── SCK → GPIO7 (ESP32-S3 Pin 11)
     │ └─────────── GND → GND (ESP32-S3 Pin 39/40)
     └───────────── VDD → 3.3V (ESP32-S3 Pin 1/2)
```

#### Important Notes

- ⚠️ **DO NOT connect VDD to 5V** - this will damage the INMP441
- The L/R pin determines which channel the mic operates on
- Grounding L/R configures it for LEFT channel
- Keep wires short (<15cm) to reduce noise
- Route away from high-current wires (speaker, power)

### Step 2: MAX98357A I2S Amplifier

The MAX98357A is a Class D amplifier that drives the speaker.

#### Pin Connections

| MAX98357A Pin | ESP32-S3 Pin | Wire Color* | Notes |
|---------------|--------------|-------------|-------|
| **VIN** | **5V (USB VBUS)** | Red | Power (3-5.5V, 5V recommended) |
| **GND** | **GND** | Black | Ground |
| **BCLK** | **GPIO4** | Yellow | I2S Bit Clock |
| **LRC** | **GPIO5** | Green | Left/Right Clock (LRCLK) |
| **DIN** | **GPIO6** | Blue | I2S Data Input |
| **GAIN** | Leave floating | - | Sets gain (floating = 15dB) |
| **SD** | Leave floating | - | Shutdown (floating = enabled) |

*Suggested wire colors*

#### Wiring Diagram - MAX98357A

```
    ┌──────────────────┐
    │    MAX98357A     │
    │  I2S Amplifier   │
    └──────────────────┘
     │ │ │ │ │ │ │ │
     │ │ │ │ │ │ │ └── GAIN (leave floating or tie to GND for 9dB)
     │ │ │ │ │ │ └──── SD (leave floating for always-on)
     │ │ │ │ │ └────── OUT- → Speaker (-)
     │ │ │ │ └──────── OUT+ → Speaker (+)
     │ │ │ └────────── DIN  → GPIO6 (ESP32-S3 Pin 10)
     │ │ └──────────── LRC  → GPIO5 (ESP32-S3 Pin 9)
     │ └────────────── BCLK → GPIO4 (ESP32-S3 Pin 8)
     └──────────────── VIN  → 5V (USB Power)
                       GND  → GND (ESP32-S3 Pin 39/40)
```

#### Gain Configuration Options

The GAIN pin determines output volume:

| GAIN Pin State | Gain Level | Best For |
|----------------|------------|----------|
| **Floating** (default) | 15dB | Most applications |
| **GND** | 12dB | Quieter environments |
| **VIN** | 9dB | Very quiet or powered speakers |

#### Shutdown (SD) Pin Options

| SD Pin State | Amplifier State | Use Case |
|--------------|-----------------|----------|
| **Floating** (default) | Always ON | Standard operation |
| **GND** | Shutdown | Power saving (requires control) |
| **GPIO pin** | Software control | Advanced power management |

#### Important Notes

- ⚠️ Speaker MUST be connected before powering on
- Running without a speaker can damage the amplifier
- 5V power recommended for best performance (3.3V works but lower volume)
- Add a 220µF capacitor between VIN and GND if experiencing power issues
- MAX98357A can get warm during operation - this is normal

### Step 3: Speaker Connection

#### Speaker Specifications

- **Impedance**: 4Ω or 8Ω (4Ω recommended)
- **Power Rating**: 2-5W
- **Type**: Full-range or midrange driver
- **Size**: 40mm (GIKFUN) or similar

#### Wiring

```
    ┌──────────────────┐
    │   MAX98357A      │
    └──────────────────┘
           │    │
         OUT+  OUT-
           │    │
           ▼    ▼
         ┌────────┐
         │ Speaker│
         │  4Ω 3W │
         └────────┘
```

| Connection | Color | Notes |
|------------|-------|-------|
| OUT+ (MAX98357A) → Speaker (+) | Usually Red | Positive terminal |
| OUT- (MAX98357A) → Speaker (-) | Usually Black | Negative/Ground terminal |

#### Important Notes

- Polarity matters for best sound quality
- Reversing polarity won't damage anything but may affect bass response
- Keep speaker wires twisted together to reduce interference
- Secure connections - vibrations can loosen wires
- Keep speaker away from microphone to prevent feedback

### Step 4: Power Distribution

#### Power Requirements

| Component | Voltage | Typical Current | Peak Current |
|-----------|---------|----------------|--------------|
| ESP32-S3 | 3.3V/5V | 80-150mA | 300mA |
| INMP441 | 3.3V | 1-2mA | 5mA |
| MAX98357A | 5V | 10mA idle | 1-2A peak |
| Total System | 5V USB | 150-200mA | 2A+ |

#### Power Connections Diagram

```
    USB-C Power (5V 2A)
         │
         ├──→ ESP32-S3 (5V/USB pin)
         │       │
         │       └──→ 3.3V LDO
         │              │
         │              ├──→ INMP441 (VDD)
         │              └──→ Internal components
         │
         └──→ MAX98357A (VIN)
                 └──→ Speaker (via amp)
    
    Ground/GND (Common)
         │
         ├──→ ESP32-S3 GND
         ├──→ INMP441 GND
         ├──→ MAX98357A GND
         └──→ USB GND
```

#### Important Notes

- ⚠️ **Minimum 2A power supply required**
- Use a quality USB power adapter (phone charger quality)
- Thin/long USB cables can cause voltage drop issues
- Add a 470µF-1000µF capacitor near MAX98357A if experiencing brown-outs
- Never connect INMP441 VDD to 5V (use 3.3V only)

### Step 5: Built-in RGB LED

The ESP32-S3 Mini has a built-in WS2812B RGB LED on GPIO47.

#### Connection

**No wiring needed!** The LED is already connected internally.

#### If Using External LED

If you want to add an external WS2812B LED strip or single LED:

| LED Pin | ESP32-S3 Pin | Notes |
|---------|--------------|-------|
| **VIN/5V** | **5V** | Power |
| **GND** | **GND** | Ground |
| **DIN/Data** | **GPIO48** | Data signal (change in YAML) |

Add a 330Ω resistor between GPIO48 and LED DIN to protect the pin.

## Complete Wiring Table

### Master Connection Table

| Component | Pin Name | ESP32-S3 Pin | Pin Number | Notes |
|-----------|----------|--------------|------------|-------|
| **Power** | | | | |
| USB Power | 5V | 5V (USB) | N/A | From USB-C |
| Common GND | GND | GND | 39, 40 | Multiple GND points |
| 3.3V Out | 3.3V | 3.3V | 1, 2 | For INMP441 |
| **INMP441 Microphone** | | | | |
| Mic Power | VDD | 3.3V | 1 or 2 | 3.3V only! |
| Mic Ground | GND | GND | 39 or 40 | |
| Mic Clock | SCK | GPIO7 | 11 | I2S Clock |
| Mic Word Select | WS | GPIO8 | 12 | L/R Clock |
| Mic Data | SD | GPIO9 | 13 | Serial Data |
| Mic Channel | L/R | GND | 39 or 40 | Left channel |
| **MAX98357A Amplifier** | | | | |
| Amp Power | VIN | 5V (USB) | N/A | 5V recommended |
| Amp Ground | GND | GND | 39 or 40 | |
| Amp Bit Clock | BCLK | GPIO4 | 8 | I2S BCLK |
| Amp L/R Clock | LRC | GPIO5 | 9 | I2S LRCLK |
| Amp Data | DIN | GPIO6 | 10 | I2S Data |
| Amp Gain | GAIN | Floating | - | 15dB (default) |
| Amp Shutdown | SD | Floating | - | Always on |
| **Speaker** | | | | |
| Speaker + | OUT+ | MAX98357A | - | From amp |
| Speaker - | OUT- | MAX98357A | - | From amp |
| **LED** | | | | |
| RGB LED | Built-in | GPIO47 | 23 | Internal |

## Visual Wiring Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                                                                 │
│                        ESP32-S3 MINI                            │
│                                                                 │
│  3.3V ──┬────────────────────────────→ INMP441 VDD             │
│         │                                                        │
│  GPIO7 ─────────────────────────────→ INMP441 SCK              │
│  GPIO8 ─────────────────────────────→ INMP441 WS               │
│  GPIO9 ─────────────────────────────→ INMP441 SD               │
│         │                                                        │
│  GPIO4 ─────────────────────────────→ MAX98357A BCLK           │
│  GPIO5 ─────────────────────────────→ MAX98357A LRC            │
│  GPIO6 ─────────────────────────────→ MAX98357A DIN            │
│         │                                                        │
│  GND ───┴──┬──┬─────────────────────→ INMP441 GND & L/R        │
│            │  │                                                  │
│            │  └───────────────────→ MAX98357A GND               │
│            │                                                     │
│  5V ───────┼─────────────────────→ MAX98357A VIN               │
│   (USB)    │                             │                      │
│            │                             ├──→ Speaker (+)       │
│            │                             └──→ Speaker (-)       │
│            │                                                     │
│  GPIO47 ───┤ (Internal RGB LED)                                │
│            │                                                     │
└────────────┴─────────────────────────────────────────────────────┘
                 │
                 └──→ Common Ground (All GND pins connected)
```

## Testing & Validation

### Step-by-Step Testing

#### 1. Visual Inspection
- [ ] Check all connections match the tables above
- [ ] Verify no exposed wires are touching
- [ ] Confirm correct voltage to each component
- [ ] Check for cold solder joints (if soldered)

#### 2. Continuity Testing (Multimeter)
- [ ] Test GND continuity across all components
- [ ] Verify no shorts between VCC and GND
- [ ] Check I2S signal line continuity

#### 3. Power-On Test (No USB connection yet)
- [ ] Set multimeter to voltage mode
- [ ] Connect USB power
- [ ] Measure 3.3V at INMP441 VDD (should read 3.2-3.4V)
- [ ] Measure 5V at MAX98357A VIN (should read 4.8-5.2V)
- [ ] Check for warm components (slight warmth is OK, hot is not)

#### 4. Firmware Upload
- [ ] Connect USB-C cable
- [ ] Flash firmware using web installer or ESPHome
- [ ] Watch for successful boot in serial monitor
- [ ] Check for I2S initialization messages

#### 5. LED Test
- [ ] RGB LED should light up during boot
- [ ] LED color indicates connection status
- [ ] Test LED effects via Home Assistant

#### 6. Microphone Test
- [ ] Speak near microphone
- [ ] Check ESPHome logs for audio activity
- [ ] Look for waveform or dB levels in logs
- [ ] Adjust gain if too quiet or clipping

#### 7. Speaker Test
- [ ] Trigger TTS response from Home Assistant
- [ ] Listen for clear audio output
- [ ] Check for distortion at high volume
- [ ] Verify no popping or clicking sounds

#### 8. Full System Test
- [ ] Say wake word
- [ ] Watch LED change to listening state
- [ ] Speak voice command
- [ ] Confirm LED shows processing state
- [ ] Hear TTS response
- [ ] Verify LED returns to idle

## Troubleshooting

### No Power / Won't Boot

**Symptoms:** No LED, no serial output
- Check USB cable is data-capable (not charge-only)
- Try different USB port or power adapter
- Measure voltage at 3.3V and 5V pins
- Check for short circuits

### Microphone Not Working

**Symptoms:** No audio captured, wake word not detected
- Verify INMP441 VDD is 3.3V (NOT 5V)
- Check L/R pin is grounded
- Confirm I2S pins are correct
- Test with higher auto_gain in config
- Check for loose connections

### No Speaker Output

**Symptoms:** Silent speaker, no TTS audio
- Verify speaker is connected
- Check MAX98357A VIN has 5V
- Confirm SD pin is floating (not grounded)
- Test speaker with multimeter (should show ~4Ω)
- Try different volume_multiplier value

### Distorted/Crackling Audio

**Symptoms:** Poor sound quality, static
- Add capacitor to MAX98357A VIN (220µF-1000µF)
- Use shorter/thicker speaker wires
- Reduce volume_multiplier
- Check power supply current capacity
- Separate speaker wires from data lines

### LED Not Working

**Symptoms:** No LED response
- Verify it's configured for GPIO47
- Check power to LED (3.3V)
- Test with simple on/off command
- Some clones use different GPIO - check docs

### Random Reboots

**Symptoms:** Device restarts unexpectedly
- Insufficient power supply (upgrade to 2A)
- Add bulk capacitor to power lines
- Reduce volume to lower peak current
- Check for voltage drop on USB cable

## Enclosure Mounting Tips

### Component Placement

1. **Microphone**: Mount near exterior hole for best audio capture
2. **Speaker**: Face speaker driver toward front opening
3. **ESP32-S3**: Position for easy USB access
4. **MAX98357A**: Place away from microphone to reduce interference

### Acoustic Considerations

- Add acoustic foam around microphone to reduce echo
- Seal speaker enclosure for better bass response
- Use grommets or rubber mounts to reduce vibration
- Keep minimum 5cm distance between mic and speaker

### Cable Management

- Use cable ties or clips to secure wiring
- Route power wires separately from signal wires
- Leave slack for assembly/disassembly
- Label connections for future maintenance

## Final Checklist

Before closing the enclosure:

- [ ] All connections secure and tested
- [ ] No exposed wires or potential shorts
- [ ] Firmware uploaded and verified
- [ ] Connected to Home Assistant
- [ ] Wake word detection working
- [ ] TTS audio clear and audible
- [ ] LED indicators functioning
- [ ] Device stable (no reboots)
- [ ] All features tested
- [ ] Enclosure fits properly

## Additional Resources

- [ESP32-S3 Mini Datasheet](https://www.wemos.cc/en/latest/s3/s3_mini.html)
- [INMP441 Datasheet](https://invensense.tdk.com/products/digital/inmp441/)
- [MAX98357A Datasheet](https://www.analog.com/media/en/technical-documentation/data-sheets/MAX98357A-MAX98357B.pdf)
- [ESPHome I2S Audio Docs](https://esphome.io/components/i2s_audio.html)
- [Home Assistant Voice Assist](https://www.home-assistant.io/voice_control/)

---

**Questions or issues?** Open an issue on GitHub or check the discussions section.
