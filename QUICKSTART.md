# Espresso Shot Timer - Quick Start Guide

Quick reference for the most important commands and workflows.

## 🚀 First Steps (when ESP32 arrives)

### 1. Create your secrets
Rename `secrets.yaml.example` to `secrets.yaml` and enter your own values (WiFi, API key, passwords):
```bash
cp secrets.yaml.example secrets.yaml
```

### 2. Connect ESP32 via USB-C

### 3. First Flash
```bash
cd espresso-shot-timer
esphome run espresso-shot-timer.yaml
```

ESPHome will ask:
- Select port (e.g. `/dev/cu.usbserial-XXXX`)
- First time: Flash via USB
- After that: OTA updates via WiFi possible

### 4. Add to Home Assistant
After first flash:
1. Home Assistant → Settings → Devices & Services
2. ESPHome integration finds "Espresso Shot Timer" automatically
3. Click "Configure"
4. Done! 🎉

## 📋 Important Commands

### ESPHome

```bash
# Validate configuration
esphome config espresso-shot-timer.yaml

# Compile (without flashing)
esphome compile espresso-shot-timer.yaml

# Flash (ESP32 must be connected)
esphome run espresso-shot-timer.yaml

# View live logs
esphome logs espresso-shot-timer.yaml

# OTA update (if already flashed)
esphome run espresso-shot-timer.yaml --device espresso-shot-timer.local
```

### Git

```bash
# Show status
git status

# Add changes
git add .

# Create commit
git commit -m "Description of change"

# Push to GitHub
git push

# Show last commits
git log --oneline -5

# Show changes since last commit
git diff
```

## ⚙️ Configuration in Home Assistant

After flash, find under **Settings → Devices & Services → ESPHome → Espresso Shot Timer**:

### Configurable Entities

| Name | Type | Description | Default |
|------|------|-------------|---------|
| **Display** | Light | Display on/off + brightness | On |
| **Start Timer** | Button | Start a new run (wakes display) | - |
| **Stop Timer** | Button | Stop (freeze) the timer | - |
| **Reset Timer** | Button | Reset and return to weather view | - |
| **Display Brightness** | Number | Display brightness | 200 |
| **Display Auto-Off** | Number | Auto-off timeout | 60s |
| **Perfect Shot Time** | Number | When timer is green | 24s |
| **Maximum Shot Time** | Number | When timer turns red | 35s |
| **Weather Update Interval** | Number | Weather refresh rate | 5min |
| **Weather View Background Opacity** | Number | Weather view opacity | 60% |
| **Timer View Background Opacity** | Number | Timer view opacity | 70% |

### Sensor Entities (Read-Only)

| Name | Type | Description |
|------|------|-------------|
| **Outdoor Temperature** | Sensor | Temperature from Home Assistant |
| **Outdoor Humidity** | Sensor | Humidity from Home Assistant |
| **Weather Condition** | Text Sensor | Weather condition from Home Assistant |
| **Timer Running** | Binary Sensor | On while the timer is counting |
| **Last Shot Time** | Sensor | Duration of the last shot (s) |
| **Display Off In** | Sensor | Countdown until display auto-off (s) |
| **Display Brightness** | Sensor | Current brightness setting |
| **Display Auto-Off** | Sensor | Current auto-off timeout |
| **Perfect Shot Time** | Sensor | Current perfect shot time |
| **Maximum Shot Time** | Sensor | Current maximum shot time |
| **Weather Update Interval** | Sensor | Current weather update interval |
| **Weather View Background Opacity** | Sensor | Current weather view opacity |
| **Timer View Background Opacity** | Sensor | Current timer view opacity |

**Note:** All sensor values appear in the Home Assistant Activity Feed for monitoring and historical data tracking.

**Tip:** **Display Off In** updates every second. Exclude it from the Recorder in your `configuration.yaml` to keep the database small (adjust the entity ID to your device name):

```yaml
recorder:
  exclude:
    entities:
      - sensor.espresso_shot_timer_display_off_in
```

### Example Settings

**Espresso (Default):**
- Perfect: 24s
- Maximum: 35s

**Ristretto:**
- Perfect: 18s
- Maximum: 25s

**Lungo:**
- Perfect: 30s
- Maximum: 45s

## 🎨 Customization

### Change Shot Times
1. Open Home Assistant
2. Settings → Devices & Services → ESPHome
3. Select "Espresso Shot Timer"
4. Adjust values:
   - **Perfect Shot Time:** 15-40s
   - **Maximum Shot Time:** 25-60s

### Change Display Auto-Off
- In Home Assistant: "Display Auto-Off" (0-300s)
- 0 = Disabled (stays always on)

### Change Brightness
**Option 1 (Live):** Home Assistant → Display → Adjust brightness

**Option 2 (Permanent):** In `espresso-shot-timer.yaml`:
```yaml
display:
  - platform: mipi_spi
    brightness: 200  # 0-255
```

### Change WiFi
In `secrets.yaml`:
```yaml
wifi_ssid: "YourNetwork"
wifi_password: "YourPassword"
```

Then reflash: `esphome run espresso-shot-timer.yaml`

## 🔧 Troubleshooting

### ESP32 not detected
```bash
# Show ports (macOS)
ls -la /dev/cu.*

# Should show: /dev/cu.usbserial-XXXX or similar
```

**Solution:**
- Change USB-C cable (some are charge-only!)
- Install CH340/CP2102 driver (usually not needed on macOS)

### Touch doesn't respond after reboot
**Known issue with FT5x06 driver**

**Solution:** Flash once more, then it works

### WiFi doesn't connect
1. Check `secrets.yaml` (SSID and password correct?)
2. ESP32 in range?
3. After 1 minute fallback hotspot activates:
   - SSID: "Espresso Shot Timer Hotspot"
   - Password: the `ap_password` from your `secrets.yaml`
   - Connect and enter new WiFi credentials

### Display stays black
```bash
# Check logs
esphome logs espresso-shot-timer.yaml
```

**Possible causes:**
- Brightness too low? → Set to 100% in HA
- Display in auto-off? → Touch display
- Initialization failed? → Reflash

### OTA update doesn't work
```bash
# Address directly with .local
esphome run espresso-shot-timer.yaml --device espresso-shot-timer.local

# Or use IP address (copy from HA)
esphome run espresso-shot-timer.yaml --device 192.168.x.x
```

**If everything fails:** Reflash via USB

### Home Assistant doesn't find ESP32
1. Is ESP32 connected to WiFi? (check logs)
2. ESPHome integration installed?
3. Manual configuration:
   - Settings → Devices & Services → Add Integration
   - Search for "ESPHome"
   - Host: `espresso-shot-timer.local` or IP address
   - Port: 6053
   - Enter API key from `secrets.yaml`

### Font/Character Issues
✅ **Fixed:** All text is now in English
- No special characters needed
- No emojis
- Basic Latin font works perfectly

### Compilation fails
**Possible causes:**
- ESPHome version too old (Minimum: 2024.6.0)
- ESP-IDF framework missing
- Network issues when downloading components

**Solution:**
```bash
# Update ESPHome
pipx upgrade esphome

# Delete build cache
rm -rf .esphome/build/

# Recompile
esphome compile espresso-shot-timer.yaml
```

## 📱 Usage

### Start timer
- Tap display (in weather mode)

### Pause/resume timer
- Tap **STOP/START button** (orange)
- Toggle function:
  - First click: Timer pauses → Button shows "START"
  - Second click: Timer resumes → Button shows "STOP"
  - Can be repeated unlimited times

### Reset timer
- Tap **RESET** (red button)
- Timer stops completely and returns to weather display
- Button text resets to "STOP"

### Control from Home Assistant
- Buttons **Start Timer**, **Stop Timer**, **Reset Timer**
- Sensors **Timer Running** and **Last Shot Time**

### Manual display on/off
- In Home Assistant: "Display" entity

## 🔐 Passwords & Secrets

**Stored in:** `secrets.yaml` (NOT in Git!). Create it by renaming `secrets.yaml.example` and entering your own values:

```yaml
wifi_ssid: "YourNetwork"
wifi_password: "YourPassword"
api_key: "GenerateYourOwnKeyWithOpenSSL"   # openssl rand -base64 32
ota_password: "ChooseAnOtaPassword"
ap_password: "ChooseAnApPassword"
```

**⚠️ IMPORTANT:** Never commit or share `secrets.yaml`!

## 📊 Hardware Pins Reference

```
Display (QSPI):
  CLK:  GPIO11
  D0:   GPIO4
  D1:   GPIO5
  D2:   GPIO6
  D3:   GPIO7
  CS:   GPIO12

Touch (I2C):
  SDA:  GPIO15
  SCL:  GPIO14
  INT:  GPIO21
  Addr: 0x38

Boot Button: GPIO0
```

**NEVER change these pins!** (Hardware-bound)

## 🆘 Help

**ESPHome logs are your friend:**
```bash
esphome logs espresso-shot-timer.yaml
```

**Complete reset:**
```bash
# Delete everything
rm -rf .esphome/

# Recompile and flash
esphome run espresso-shot-timer.yaml
```

## 📚 Further Links

- [ESPHome Documentation](https://esphome.io/)
- [LVGL Documentation](https://docs.lvgl.io/)
- [GitHub Repository](https://github.com/IamTheLoki/espresso-shot-timer)
- [Home Assistant Forum Thread](https://community.home-assistant.io/t/esp32-s3-1-8inch-amoled-touch/956270)

---

**Good luck with your perfect espresso! ☕**
