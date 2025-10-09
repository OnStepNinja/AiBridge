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


---


### 4. Upload Firmware

How to flash the firmware to ESP32

1. Install Python and esptool:
   pip install esptool

2. Connect the ESP32 board to your PC via USB. 
   Check the COM port number (e.g., COM3).

3. Place the following three files in the same folder:
   - AiBridge_v7_10.ino.bootloader.bin
   - AiBridge_v7_10.ino.partitions.bin
   - AiBridge_v7_10.ino.bin

4. Open Command Prompt, move to the folder, and run:

   esptool --chip esp32 --port COM3 --baud 921600 --before default_reset --after hard_reset write_flash -z --flash_mode dio --flash_freq 80m --flash_size detect 0x1000 AiBridge_v7_10.ino.bootloader.bin 0x8000 AiBridge_v7_10.ino.partitions.bin 0x10000 AiBridge_v7_10.ino.bin

*Replace "COM3" with your actual port number.

5. Wait for the process to complete. 

---

### 5. Initial Configuration

**First Boot (AP Mode)**

1. ESP32 creates WiFi network: `AiBridge5000_AP`
2. Connect to it (password: `password`)
3. Open browser → `http://192.168.4.1`
4. Enter your WiFi information and configure it from the File Manager.
5. ESP32 reboots and connects to your network

**The AiBridge Console displays two IP addresses**

- AP Mode: http://192.168.4.1
- STA Mode: Your IP address (DHCP)

---

## 🎮 Usage

### Web Interface

Open browser → `http://[IP-ADDRESS]`

**Manual Control Mode**:
- Direct telescope control via web UI
- Real-time status display

### AI Control (Comet Browser)

**Setup Comet Browser**:
1. Open Comet Browser by Perplexity AI
2. Tell AI your IP: "My telescope is at http://[IP-ADDRESS]/api/spec"
3. Start commanding naturally!


**Comet Browser Features

AI Agent-Based Browser
The browser hosts a constantly present AI assistant that can autonomously perform direct operations on webpages (such as clicking, inputting, navigating, summarizing, etc.) based on user instructions.

Real-time Screen Recognition and Automated Actions
It recognizes the structure and content of the displayed webpage in real time, allowing for the automation of actions like button clicks, form submissions, and social media posting.

Agentic Search
Beyond simple information retrieval, it can fully complete complex, multi-service tasks such as "plan a trip" or "create a summary table."

Hybrid Operation
Users can switch between the AI's automated control and their own manual control. The target of the AI's operation is highlighted on the screen while the AI is in control.

External Integration and Voice Control Support
Standard features include integration with various services like Gmail and Calendar, as well as support for voice assistants.


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

**🌟 First Release -  🌟**

Made with ❤️ for the astronomy community  
*Feedback appreciated!*

</div>

---

## 📦 ファームウェア

ファームウェアは firmware フォルダ内にあります。MITライセンスの元、自由に利用できます。
