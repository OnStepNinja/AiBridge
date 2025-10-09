# AiBridge
# 🔭 AiBridge - Talk to Your Telescope

[![Version](https://img.shields.io/badge/version-7.10-blue.svg)] [![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

**Control your OnStep mount through natural conversation with AI (Comet Browser)**

---

## 🌟 What is AiBridge?

**The first telescope interface designed for conversational AI control via Perplexity AI's Comet Browser.**

Simply talk to the AI - **in natural language, via text or voice** - and let the AI:

- 🧠 **Understand** your intent ("Show me the Andromeda Galaxy")
- 💭 **Reason** about coordinates, timing, and positioning
- 🎯 **Execute** telescope commands automatically  
- 🗣️ **Respond** with status updates in conversation

**No manual coordinate entry. No complex commands. Just conversation.**

### Current AI Platform Support

**✅ Perplexity AI - Comet Browser** (Primary support)

- Full local network access capability
- Can directly call AiBridge REST API
- Optimized for conversational telescope control
- **Currently the only AI agent with local network access**

**Platform**: ESP32 | **AI**: Comet Browser required for conversational control

---

🎯 **How It Works**

You: "Point my telescope at Jupiter"
  ↓
  Comet AI (Thinks): "Jupiter is currently at RA 05:23:15, DEC +22:47:30"
  ↓
  Comet AI (Reasons): "I'll send a GOTO command to the telescope"
  ↓
  Comet AI (Executes): Sends GET request
  GET /api/goto?ra=05:23:15&dec=+22:47:30
  ↓
  Comet AI (Responds): "Telescope is slewing to Jupiter."

---

## ✨ Key Features

### AI-First Design
- 🗣️ **Natural Language Control** - Text or voice commands
- 🧠 **AI Reasoning Integration** - Let AI handle the technical details
- 🎯 **Context Awareness** - AI understands astronomy concepts
- 📍 **Smart Object Resolution** - "Andromeda" → M31 → RA/DEC automatically

### Web Interface
- 📱 **Responsive Design** - Works on phone, tablet, or desktop
- 🌐 **Zero Installation** - Just open browser and connect
- 🔄 **Real-time Updates** - Live status via REST API
- 🎨 **Dark Mode** - Astronomy-friendly interface

### Technical
- ⚡ **ESP32 Based** - Fast, reliable, cost-effective
- 🔌 **LX200 Serial** - Compatible with OnStep and other mounts
- 🌐 **REST API** - Full telescope control via HTTP
- 📡 **WiFi Ready** - AP mode and Station mode support
- 🔓 **ASCOM Alpaca** - Standard astronomy protocol (coming soon)

---

## 🚀 Quick Start

### What You Need

#### Hardware
- ESP32 development board (ESP32-DevKitC or similar)
- OnStep telescope mount (or LX200-compatible mount)
- 3.3V TTL serial connection (TX/RX)
- USB cable for programming
- Power supply for ESP32 (5V via USB or regulated power)

#### Software
- Arduino IDE 1.8.x or 2.x
- ESP32 board support package
- Required libraries (see Installation)

---

## 📥 Installation

### 1. Arduino IDE Setup

```bash
# Add ESP32 board support
# File → Preferences → Additional Board Manager URLs:
https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json

# Tools → Board → Boards Manager → Search "ESP32" → Install
```

### 2. Install Required Libraries

Via Arduino Library Manager:

- **ESPAsyncWebServer** by lacamera
- **AsyncTCP** by dvarrel  
- **ArduinoJson** by Benoit Blanchon

```bash
# Tools → Manage Libraries → Search and install each
```

### 3. Hardware Wiring

```
OnStep Mount        ESP32
────────────────────────────
TX      →           GPIO16 (RX2)
RX      ←           GPIO17 (TX2)
GND     ─           GND
```

⚠️ **Important**: 
- Use 3.3V TTL serial (not RS232!)
- Cross TX→RX and RX→TX
- Common ground required

### 4. Upload Firmware

```bash
1. Open AiBridge.ino in Arduino IDE
2. Tools → Board → ESP32 Dev Module
3. Tools → Port → Select your ESP32 port
4. Upload
```

### 5. Initial Configuration

**First Boot (AP Mode)**

1. ESP32 creates WiFi network: `AiBridge-XXXX`
2. Connect to it (password: `12345678`)
3. Open browser → `http://192.168.4.1`
4. Configure your WiFi credentials
5. ESP32 reboots and connects to your network

**Find Your IP Address**

- Check your router's DHCP table
- Or use serial monitor (115200 baud)
- Or use network scanner app

---

## 🎮 Usage

### Web Interface

Open browser → `http://[ESP32-IP-ADDRESS]`

**Manual Control Mode**:
- Direct telescope control via web UI
- Slew to coordinates
- Park, home, tracking controls
- Real-time status display

### AI Control (Comet Browser)

**Setup Comet Browser**:
1. Open Comet Browser by Perplexity AI
2. Tell AI your ESP32 IP: "My telescope is at http://192.168.1.100"
3. Start commanding naturally!

**Example Voice Commands**:

```
"Point my telescope at Jupiter"
"Show me where Andromeda Galaxy is"
"Is my telescope tracking right now?"
"Park the mount please"
"What's the status of my telescope?"
```

**The AI will**:
- Look up current object coordinates
- Calculate proper RA/DEC
- Send commands to AiBridge
- Report status back to you
- Handle errors gracefully

---

## 🔌 REST API Reference

Base URL: `http://[ESP32-IP]/api`

### Endpoints

#### Status
```http
GET /api/status
```
Returns current mount status, coordinates, tracking state

#### GOTO
```http
GET /api/goto?ra=HH:MM:SS&dec=±DD:MM:SS
```
Slew telescope to specified coordinates

#### Tracking
```http
GET /api/tracking?state=[on|off]
```
Enable or disable sidereal tracking

#### Park
```http
GET /api/park
```
Park the telescope at park position

#### Home
```http
GET /api/home
```
Slew telescope to home position

### Response Format

```json
{
  "success": true,
  "message": "Slewing to target",
  "data": {
    "ra": "05:23:15",
    "dec": "+22:47:30",
    "tracking": true
  }
}
```

---

## ⚙️ Configuration

Edit `config.h` before uploading:

```cpp
// WiFi Settings
#define WIFI_SSID "YourWiFiName"
#define WIFI_PASSWORD "YourPassword"

// Serial Settings  
#define SERIAL_BAUD 9600        // OnStep default
#define RX_PIN 16               // ESP32 RX pin
#define TX_PIN 17               // ESP32 TX pin

// Server Settings
#define WEB_PORT 80             // HTTP port
#define AP_SSID "AiBridge"      // AP mode SSID
#define AP_PASSWORD "12345678"  // AP mode password
```

---

## 🐛 Troubleshooting

### Can't Connect to WiFi
- Ensure 2.4GHz network (ESP32 doesn't support 5GHz)
- Check SSID and password in config
- Try AP mode: Connect to `AiBridge-XXXX`
- Monitor serial output (115200 baud)

### LX200 Commands Not Working
- Verify TX/RX wiring (not swapped?)
- Check baud rate matches OnStep (default 9600)
- Test with serial monitor first
- Ensure 3.3V TTL levels (not RS232)

### Web Interface Loads But Commands Fail
- Check serial connection to mount
- Verify mount is powered on
- Test mount with other software (ASCOM, Stellarium)
- Check serial monitor for error messages

### AI Can't Control Telescope
- Verify you're using Comet Browser
- Confirm ESP32 IP address is correct
- Ensure ESP32 is on same local network
- Try manual API test: `http://[IP]/api/status`
- Check firewall settings

### General Tips
- Use AP mode: http://192.168.4.1
- Check WiFi is 2.4GHz (ESP32 doesn't support 5GHz)

**LX200 not working?**
- Verify TX/RX connections (not swapped)
- Check baud rate: 9600

---

## 💖 Support This Project

If you find AiBridge useful, please consider:

- ⭐ **Star this repository**
- 💰 **Donate**: https://paypal.me/OnStepNinja
- 🐛 **Report bugs** or suggest features
- 📢 **Share** with the astronomy community

Your support helps continue development! 🙏

---

## 📜 License

**MIT License** - see [LICENSE](LICENSE) file

Copyright (c) 2025 Nishioka Sadahiko

---

## 🔗 Links

- 🌐 **Website**: https://nskikaku.blog.fc2.com/
- 💻 **GitHub**: https://github.com/OnStepNinja
- 🔭 **OnStep Project**: https://onstep.groups.io
- 📧 **Contact**: nishioka.sst@gmail.com

  All product names, trademarks, and registered trademarks are the property of their respective owners.

---

## 🙏 Acknowledgments

Special thanks to:

- **OnStep Team** - For the excellent telescope control firmware
- **ASCOM Initiative** - For the Alpaca protocol
- **Perplexity AI** - For Comet Browser's local network capabilities
- **ESP32 Community** - For Arduino core and libraries

---

<div align="center">

**🌟 First Release - More Updates Coming Soon! 🌟**

Made with ❤️ for the astronomy community  
*Feedback appreciated!*

</div>

---

## 📦 ファームウェア

ファームウェアは firmware フォルダ内にあります。MITライセンスの元、自由に利用できます。
