A# AiBridge

# 🔭 AiBridge - Talk to Your Telescope

[![Version](https://img.shields.io/badge/version-7.9-blue.svg)](https://github.com/OnStepNinja/AiBridge/releases)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

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

## 🎯 How It Works

You: "Point my telescope at Jupiter"
                              ↓
Comet AI: [Thinks] "Jupiter is currently at RA 05:23:15, DEC +22:47:30"
                              ↓
          [Reasons] "I'll send a GOTO command to the telescope"
                              ↓
          [Executes] GET /api/goto?ra=05:23:15&dec=+22:47:30
                              ↓
          [Responds] "Telescope is slewing to Jupiter."
---

## ✨ Key Features

### AI-First Design
- 🗣️ **Natural Language Control** - Text or voice commands
- 🧠 **AI Reasoning Integration** - Let AI handle the technical details
- 💬 **Conversational Interface** - Ask questions, get guided help
- 🌍 **Multi-language Support** - Works in any language AI supports

### Core Functionality
- ✅ REST API with OpenAPI 3.0 specification
- ✅ Full ASCOM/Alpaca server support
- ✅ Dual LX200 command ports (9999, 9998)
- ✅ Alpaca device discovery (auto-detect other telescopes)
- ✅ Real-time telescope status monitoring
- ✅ WiFi AP + Station dual mode (DHCP support)

### Advanced Features
- 🎨 **Dual UI System** - Hardware switch (GPIO33) for instant UI switching
  - **GPIO33 HIGH (default)**: AiBridge Console (Standard control panel,`index.html`)
  - **GPIO33 LOW**: Custom UI (`index2.html`)
- 📁 **SPIFFS File System** - ~1.3MB storage for user files
- 💻 **Programmable Interface** - Create custom HTML/JavaScript apps
- 📤 **Secure File Manager** - Hardware-protected file upload system
  - ⏱️ **30-minute activation window** after boot
  - 🔘 **Hardware button required** for access (10-minute sessions)
  - 💡 **LED indicator** shows access status
  - 🔒 **Automatic lockout** prevents unauthorized modifications
- 🛠️ **Developer Friendly** - Full API access for custom applications

---

## 📦 What's Included

This release includes:

- ✅ **Compiled firmware** (`AiBridge_v7.9.bin`)
- ✅ **API specification** (`openapi.json`)(for SPIFFS upload)
- ✅ **Sample Web UI files** (for SPIFFS upload)

**Note**: Source code (.ino) is not included in this distribution. The MIT License does not require source code disclosure.

---

## 🚀 Quick Start

### Step 1: Hardware Setup

**Minimum Requirements**:
- ESP32 development board
- serial / RS-232 level converter
- OnStep-compatible telescope mount


### Step 2: Flash Firmware
```bash
esptool.py --chip esp32 --port COM3 --baud 921600 \
  write_flash 0x10000 firmware/AiBridge_v7.9.bin
```

### Step 4: Network Setup

1. **Power on** - ESP32 creates WiFi access point
2. **Connect to AP**:
   - SSID: `AiBridge5000_AP`
   - Password: `password`
3. **Open browser**: http://192.168.4.1
4. **Configure WiFi**: Use File Manager to set your home network
5. **Restart** - Now accessible on your home network

### Step 5: AI Setup (Comet Browser)

1. Install Comet Browser from Perplexity AI
2. Ensure computer is on same network as AiBridge
3. Open Comet Browser and say:
```
   "Access my telescope API at http://[your-ip]/openapi.json 
    and help me control it"
```

### Step 6: Start Observing!
```
You: "Show me Saturn"
AI: [Points telescope at Saturn automatically]

You: "What else is interesting tonight?"
AI: [Suggests targets based on current sky conditions]
```

## 🤖 AI Integration with Comet Browser

### Why choose Comet Browser?
- ✅ Local network access (192.168.x.x addresses)
- ✅ Direct API calls, no cloud proxy required
- ✅ Enhanced privacy — no data sent to the cloud

**Limitations of other AI tools (as of now):**
- ❌ No local network API access
- ❌ Require cloud-accessible endpoints
- ❌ Limited device control

---

## 🌐 Network Access

### Access Point Mode (Default)
```
SSID: AiBridge5000_AP
Password: password
URL: http://192.168.4.1/

API Spec: http://192.168.4.1/openapi.json
LX200 Ports: 9999, 9998

```

### Station Mode (After WiFi setup)
```
IP: [DHCP assigned]
AiBridge Console: http://[your-ip]/

API Spec: http://[your-ip]/openapi.json
LX200 Ports: 9999, 9998
```

## 📖 API Examples

### REST API
```http
# Get telescope status
GET /api/status

# GOTO command
GET /api/goto?ra=12:30:45&dec=+45:30:00

# Send LX200 command
GET /api/command?cmd=GR

# Emergency stop
GET /api/stop
```

### ASCOM/Alpaca (Standard)
```http
GET /api/v1/telescope/0/rightascension
GET /api/v1/telescope/0/declination
PUT /api/v1/telescope/0/slewtocoordinatesasync
```

**Complete API documentation**: See [openapi.json](data/openapi.json)

---

## 🔄 Dual UI System

### Hardware UI Switching

**UI selection via GPIO33 at boot time**:

| GPIO33 State | UI Loaded | Use Case |
|--------------|-----------|----------|
| **HIGH** (default) | `index.html` | AiBridge Console |
| **LOW** (grounded) | `index2.html` | Custom user interface |

### Hardware Connection
```
GPIO33 ──┬── [Switch] ── GND
         └── (Internal pull-up - no external resistor needed)
```

**On NS-5000 Hardware**: Use the dedicated DU (Dual UI) pin connector

### Use Cases

- **Observatory with multiple users** - Beginner vs. Advanced interfaces
- **Different observation modes** - Visual vs. Astrophotography UIs
- **Development/Production** - Testing vs. Stable interfaces

---

## 🎨 Custom UI Development

### Creating Your Interface

Upload custom HTML/JavaScript files to create your own control interface.

**Storage**: ~1.3MB SPIFFS

**Example Custom UI**:(save as index2.html):
```html
<!DOCTYPE html>
<html>
<head>
    <title>My Telescope Control</title>
</head>
<body>
    <h1>Custom Control Panel</h1>
    <button onclick="getStatus()">Get Status</button>
    <div id="status"></div>
    
    <script>
    async function getStatus() {
        const response = await fetch('/api/status');
        const data = await response.json();
        document.getElementById('status').innerHTML = 
            `RA: ${data.data.ra}<br>DEC: ${data.data.dec}`;
    }
    </script>
</body>
</html>
```

Important: Set GPIO33 to LOW before uploading index2.html to enable File Manager access.


---

## ⚠️ Current Limitations

### AI Platform Support
- **Currently requires**: Perplexity AI's Comet Browser
- **Reason**: Only AI with local network API access capability
- **Future**: Will support other platforms when they add local network features

### Alternative Access Methods
If you cannot use Comet Browser, AiBridge still works with:
- ✅ Web browser (manual control panel)
- ✅ ASCOM/Alpaca compatible software (NINA, Stellarium, etc.)
- ✅ Direct LX200 protocol (ports 9999, 9998)
- ✅ REST API (any HTTP client)
- ✅ Custom web UI (programmable interface)

---

## 📝 Current Status

**Working Features** ✅:
- OnStep telescope control
- REST API
- ASCOM/Alpaca server
- Web control panel
- Alpaca device discovery
- WiFi configuration
- AI control via Comet Browser

**Incomplete Features** ⚠️:
- Some advanced features still in development
- Documentation being expanded

**This is a working, usable system** - but active development continues!

---

## 🐛 Support

### Getting Help
- 📋 **GitHub Issues**: [Report bugs or ask questions](https://github.com/OnStepNinja/AiBridge/issues)
- 📧 **Email**: nishioka.sst@gmail.com
- 💬 **Forum**: [OnStep Groups](https://onstep.groups.io)

### Troubleshooting

**Can't connect to WiFi?**
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
