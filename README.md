A# AiBridge

# 🔭 AiBridge - AI-Controllable Telescope Interface

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

### Traditional vs. AiBridge

**Traditional telescope control**:
```
User → Manual coordinate lookup → Manual entry → Command execution
      (Complex, error-prone, requires astronomy knowledge)
```

**AiBridge with Comet Browser**:
```
User → Natural conversation → AI reasoning → Automatic execution
      (Simple, intuitive, no technical knowledge needed)
```

### Example Conversation
```
You: "Point my telescope at Jupiter"
                              ↓
Comet AI: [Thinks] "Jupiter is currently at RA 05:23:15, DEC +22:47:30"
                              ↓
          [Reasons] "I'll send a GOTO command to the telescope"
                              ↓
          [Executes] GET /api/goto?ra=05:23:15&dec=+22:47:30
                              ↓
          [Responds] "Telescope is slewing to Jupiter."
```

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
  - **GPIO33 HIGH (default)**: Standard control panel (`index.html`)
  - **GPIO33 LOW**: Custom UI (`index2.html`)
- 📁 **SPIFFS File System** - ~1.3MB storage for user files
- 💻 **Programmable Interface** - Create custom HTML/JavaScript apps
- 📤 **Secure File Management** - Hardware-protected file upload system
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
- ✅ **Web UI files** (for SPIFFS upload)

**Note**: Source code (.ino) is not included in this distribution. The MIT License does not require source code disclosure.

---

## 🚀 Quick Start

### Step 1: Hardware Setup

**Minimum Requirements**:
- ESP32 development board
- MAX3232 RS-232 level converter
- OnStep-compatible telescope mount

**Wiring**:
```
ESP32    MAX3232    OnStep
TX0  →   RIN    →   RX
RX0  ←   TOUT   ←   TX
GND  →   GND    →   GND
```

### Step 2: Flash Firmware
```bash
esptool.py --chip esp32 --port COM3 --baud 921600 \
  write_flash 0x10000 firmware/AiBridge_v7.9.bin
```

### Step 3: Upload SPIFFS Data

- Use Arduino IDE: Tools → ESP32 Sketch Data Upload
- Files located in `/data` folder

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

---

## 🤖 AI Integration via Comet Browser

### Real Conversation Examples

#### Example 1: Simple Object Finding
```
You: "I want to see the Orion Nebula"

Comet AI: "The Orion Nebula (M42) is a beautiful target! Let me point 
           your telescope there. It's currently at RA 05:35:17, 
           DEC -05:23:28."
         
           [Calls: GET /api/goto?ra=05:35:17&dec=-05:23:28]
         
           "Your telescope is now slewing to M42. It should arrive in 
           about 25 seconds. This is a great target for visual 
           observation!"
```

#### Example 2: Voice Control
```
You (speaking): "What's the telescope pointing at right now?"

Comet AI: [Calls: GET /api/status]
    
          "Your telescope is currently pointed at RA 12h 30m 15s, 
          DEC +12° 23' 00". That's in the constellation Virgo, 
          near the Virgo Cluster of galaxies."
```

#### Example 3: Complex Reasoning
```
You: "I want to photograph a galaxy that's high in the sky"

Comet AI: "Let me find a good target for you. I recommend M81 
           (Bode's Galaxy). It's currently at altitude 65° - 
           perfect for imaging with minimal atmospheric distortion.
         
           Shall I point your telescope there?"

You: "Yes"

Comet AI: [Calls: GET /api/goto?ra=09:55:33&dec=+69:03:55]
        
          "Slewing to M81 now. For astrophotography, I recommend 
          3-5 minute exposures."
```

### Why Comet Browser?

**Technical advantage**:
- ✅ Can access local network (192.168.x.x addresses)
- ✅ Direct API calls without cloud proxy
- ✅ Low latency communication
- ✅ Works offline (local network only)
- ✅ Privacy - no data sent to cloud

**Other AI limitations** (as of now):
- ❌ Claude Desktop: No local network API access
- ❌ ChatGPT: Requires cloud-accessible endpoints
- ❌ Google Gemini: Limited local device control

---

## 🌐 Network Access

### Access Point Mode (Default)
```
SSID: AiBridge5000_AP
Password: password
URL: http://192.168.4.1/
```

### Station Mode (After WiFi setup)
```
IP: [DHCP assigned]
Control Panel: http://[your-ip]/
LX200 Ports: 9999, 9998
API Spec: http://[your-ip]/openapi.json
```

---

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
| **HIGH** (default) | `index.html` | Standard control panel |
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

**Example Custom UI**:
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

**Upload as `index2.html`** and switch GPIO33 to LOW on boot.

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
