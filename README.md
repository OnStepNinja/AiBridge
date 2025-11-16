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
- 🔓 **ASCOM Alpaca** - Standard astronomy protocol

---

## 🚀 Quick Start

### What You Need

#### Hardware
- ESP32 development board (ESP32-DevKitC or similar)
- OnStep telescope mount (or LX200-compatible mount)
- 3.3V TTL serial connection (TX/RX)
- USB cable for programming


---


### Upload Firmware

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

### Initial Configuration

**First Boot (AP Mode)**

1. ESP32 creates WiFi network: `AiBridge5000_AP`
2. Connect to it (password: `password`)
3. Open browser → `http://192.168.4.1`
4. Enter your WiFi information and configure it from the File Manager.
5. ESP32 reboots and connects to your network

**The AiBridge Console displays two IP addresses**

- AP Mode: http://192.168.4.1
- STA Mode: Your IP address (DHCP)


### Hardware Connections
- **OnStep Serial**: ESP32 default UART0 (TX: GPIO1, RX: GPIO3)
- **Debug Serial**: GPIO16 (RX), GPIO17 (TX) - Optional
- **File Manager Enable**: GPIO39 (with pull-up resistor)
- **Custom UI Select**: GPIO33 (internal pull-up)
- **LED Indicator**: GPIO25


## 🔒 Security Features

### File Manager Protection
- Hardware button (GPIO39) required for access
- 30-minute window after boot
- 10-minute sessions when activated
- LED indicator shows access status
- Automatic lockout after 30 minutes


### Custom UI Support
- GPIO33 LOW at boot: Load custom index2.html
- GPIO33 HIGH (default): Standard AiBridge Console


### WiFi Configuration Steps
1. Enable File Manager (press GPIO39 button within 30min of boot)
2. Navigate to File Manager section
3. Enter SSID and Password in WiFi Configuration
4. Click "Save Config"
5. Restart ESP32 to apply changes


### Network Ports
- **HTTP Web Interface**: Port 80
- **LX200 Command Ports**: 9999, 9998 (TCP)
- **Alpaca Discovery**: UDP 32227


### Storage (SPIFFS)
- **Capacity**: ~1.3MB for user files
- **Supported Files**: HTML, JavaScript, CSS, JSON
- **Custom Web Apps**: Upload your own control interface


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
2. Tell AI your IP: "My telescope is at http://[IP-ADDRESS]/openapi.json   or  /api/spec"
3. Start commanding naturally!

**Comet Browser can:**
- Autonomously control web interfaces
- Execute complex multi-step tasks
- Integrate with local network devices
- Support voice commands


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

## Info & Updates

**2024-11-16 AiBridge v7.13 - OnStep Stability Fix** 🎯
**Major stability improvements for OnStep controllers. Essential update.**

* **Fixed**: RA/DEC coordinate display issues with OnStep (incomplete/incorrect values)
* **Fixed**: Serial communication timeout issues (30ms→100ms on command ports)
* **Added**: Support for OnStep high-precision coordinate format (asterisk delimiter)
* **Improved**: Buffer management and mutex timeouts for reliable communication

**Root Cause**: OnStep's 50-100ms response time exceeded previous 30ms timeout, causing command conflicts.
**Impact**: OnStep users experience dramatically improved stability. NS-5000 operation unaffected.
**Priority**: High - Recommended for all OnStep users.


2025-10-31 AiBridge v7.12.1 - Custom UI Restoration & Version Display 🚀
Critical fix restoring Custom UI functionality. Custom HTML pages (index2.html, etc.) now load properly again.
Control panel now shows version number automatically in the title (e.g. "AiBridge Console v7.12.1").
Users of v7.12 should upgrade immediately to avoid UI errors.


**2025-10-30 AiBridge v7.12 - ASCOM Alpaca Protocol Compliance Update** 🎯  
Major improvements to ASCOM/Alpaca compatibility based on official protocol validation:
* **Zero Errors Achievement** - Passed ASCOM Check Alpaca Protocol with 0 errors (previously 1 error, 95 issues)
* **Required Endpoints Implementation** - Added InterfaceVersion, Name, Description, DriverInfo, DriverVersion, SupportedActions
* **ClientTransactionID Fix** - Full case-insensitive parameter handling per ASCOM standard
* **Error Handling Enhancement** - Proper Alpaca error responses (ErrorNumber 1024) for unimplemented methods
* **AtPark Property** - Basic implementation for protocol compliance
* **Improved Compatibility** - Better integration with NINA, Stellarium, and other ASCOM-compatible software
* **Stable Release** - Production-ready with enhanced reliability


2025-10-27 AiBridge_ESP32_C3 v7.11 Public Release 🚀
We are excited to announce the public release of the new AiBridge_ESP32_C3 v7.11 firmware version.

Core Architectural Change: The CPU has been switched from the standard ESP32 to the cost-effective and compact ESP32-C3. This means the internal core architecture is now RISC-V instead of Xtensa.

High Compatibility: The operation methods, source code, and overall usage are largely compatible with the original ESP32 firmware, allowing for a smooth transition and minimal changes to existing setups.

Important Notice for Japan: This AiBridge_ESP32_C3 board is currently not certified with the Giteki Mark (Technical Conformity Mark). Therefore, domestic use in Japan is prohibited. We are actively exploring an alternative version using the ESP32-PICO to ensure compliance for our Japanese users.


2025-10-22 ### v7.11 Version Update Contents
- PHD2 Support - Full autoguiding compatibility
- Alpaca Switch Feature - 4-channel relay control (ON/OFF) via GPIO27/14/12/13
- ObservingConditions Implementation - DHT22 temperature/humidity sensor support (GPIO5), 60-second updates
- Location Name Feature - Location Name added to WiFi settings, automatic Ai- prefix for improved multi-AiBridge identification
- Alpaca Device Expansion - 3-device support (Telescope/ObservingConditions/Switch)
- Full Backward Compatibility - Safe to upgrade with confidence
- Practical Functionality - Ready to use in actual observations


2025-10-16 AiBridge Additional Information  
  We have discovered that GPIO39 (FILE_MANAGER_ENABLE_PIN) shown in the schematic does **not have an internal pull-up resistor enabled**.  
  **Please connect an external pull-up resistor to this pin.**

---

## 💖 Support This Project

If you find AiBridge useful, please consider:

- ⭐ **Star this repository**
- 💰 **Donate**: https://paypal.me/OnStepNinja
- 🐛 **Report bugs** or suggest features
- 📢 **Share** with the astronomy community

Your support helps continue development! 🙏


### :speech_balloon: Discussionsタブのご案内 / Join our Discussions!


**ご質問・ご意見・提案は [Discussions](https://github.com/OnStepNinja/AiBridge/discussions) タブで受け付けています。どなたでもお気軽に投稿してください！**

You can ask questions, make suggestions, and join our community on the [Discussions](https://github.com/OnStepNinja/AiBridge/discussions) tab. Everyone is welcome!

[![Discussionsで話しましょう / Join our Discussions](https://img.shields.io/badge/Discussions-参加する%20/%20Join%20Us-blue?logo=github)](https://github.com/OnStepNinja/AiBridge/discussions)

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

## 📦 Firmware
The firmware files are located in the firmware folder. Free to use under MIT License.
ファームウェアは firmware フォルダ内にあります。MITライセンスの元、自由に利用できます。
まだまだプロトタイプですが、お試しください。ご意見、ご希望などありますと大変参考になります。

