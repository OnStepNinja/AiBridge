# AI-Native Embedded Observatory Control
### Bridging Legacy Hardware and Direct-to-Silicon AI Integration

This project represents a comprehensive ecosystem designed to connect astronomical control systems with Large Language Models (LLMs), evolving from a specific need to overcome modern browser security restrictions into a general-purpose AIoT solution.

---

## 🧠 AiBridge
**AI Interface Foundation for ASCOM / Alpaca / OnStepNinja Systems**

AiBridge is the core integration platform designed to connect existing astronomical control systems with LLMs. By integrating ASCOM, Alpaca, and OnStepNinja environments, it enables intuitive natural-language control of astronomical equipment.

* **Repository:** [https://github.com/OnStepNinja/AiBridge](https://github.com/OnStepNinja/AiBridge)

### Core Roles
* Bridging LLMs with astronomical control hardware.
* Seamless integration with ASCOM / Alpaca systems.
* Serving as the central layer for AI-driven observatory automation.

---

## 🔭 AiBridgeMCP
**Native ESP32 Model Context Protocol (MCP) Server**

AiBridgeMCP is a standalone Model Context Protocol implementation running entirely on the ESP32 microcontroller. It connects AI agents directly to AiBridge, ASCOM, Alpaca, and OnStepNinja systems.

* **Repository:** [https://github.com/OnStepNinja/AiBridgeMCP](https://github.com/OnStepNinja/AiBridgeMCP)
* **MCP Market:** [AiBridge Server Page](https://mcpmarket.com/ja/server/aibridge?utm_source=chatgpt.com)
* 
### Development Background & The "Comet" Solution
The development of AiBridgeMCP began as a practical solution to an unexpected limitation. **Due to tighter security restrictions in the Comet browser, direct local network access to the original AiBridge system became blocked.**

To resolve this connectivity issue, I developed this MCP server as an alternative pathway. What started as a workaround to bridge AI models with hardware evolved into a standalone infrastructure. By running the MCP server natively on an ESP32, we eliminate the need for a proxy PC, allowing the system to bypass browser restrictions and communicate directly via SSE (Server-Sent Events).

### Key Features
* **Browser-Independent:** Overcomes local network access restrictions (e.g., Comet browser blocks) by using the standardized Model Context Protocol.
* **Standalone Operation:** Runs entirely on a single ESP32 microcontroller without a host PC.
* **Direct AI Control:** Enables agents like Claude, ChatGPT, or Gemini to interpret commands (e.g., "Capture M31", "Track Jupiter") and control hardware directly.
* **Expandable:** While built for astronomy, this architecture serves as a foundation for general IoT and industrial AI applications.

---

## ⚙️ OnStepNinja
**Hybrid Automatic GOTO Control System**

A high-precision automatic GOTO control system combining the strengths of OnStep and NS-5000 technologies. OnStepNinja transforms conventional equatorial mounts into AI-native observatory systems.

* **Repository:** [https://github.com/OnStepNinja/OnStepNinja](https://github.com/OnStepNinja/OnStepNinja)
* **Official Release:** [https://onstepninja.booth.pm/items/7944168](https://onstepninja.booth.pm/items/7944168)

### Key Features
* **Hybrid Architecture:** Combines OnStep NS-3000 (Optimized for AI) + NS-5000.
* **High-Precision Control:** Advanced stepper motor management.
* **Integrated Plate Solving:** Native support for precise positioning.
* **License:** Released under the GNU General Public License v3 (GPLv3).

---

## 🚀 Future Outlook: Beyond Astronomy

While currently focused on ASCOM and Alpaca applications, **AiBridgeMCP** represents an emerging foundational technology. Implementing an MCP server on a microcontroller proves that this is not merely a niche solution for astronomy, but a core infrastructure layer for AI-integrated systems.
[AiBridge on MCP Market](https://mcpmarket.com/ja/server/aibridge?utm_source=chatgpt.com)

We view this as an opportunity to expand the concept globally, applying this "Direct-to-Silicon" AI control to IoT, robotics, and industrial automation fields.
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

2026-02-13: AiBridgeMCP Listed on MCP Market - World's First MCP Server on ESP32
AiBridgeMCP has been featured on MCP Market as the world's first and only implementation of a Model Context Protocol (MCP) server running natively on an ESP32 microcontroller.

This is a true standalone solution: No Raspberry Pi, no PC proxy—just a $5 board communicating directly with Claude over SSE to bridge AI agents and telescope hardware via ASCOM Alpaca.
Please refer to the listing page for more details: https://mcpmarket.com/ja/server/aibridge

## 2026-02-7: 🔔 New: AiBridgeMCP – Direct AI Control via MCP (No Proxy Required)
AiBridge now supports **AiBridgeMCP**, a lightweight standalone **MCP (Model Context Protocol) server running directly on ESP32**.
With **direct SSE (Server-Sent Events) transport**, AI agents such as **Claude, Gemini, or ChatGPT** can connect directly to AiBridge over the local network, **without any PC-side proxy, Python, or Node.js runtime**.
This enables **AI-to-hardware interaction** with telescope control systems via the **ASCOM Alpaca protocol**, making AiBridge an **AI-native telescope controller**.
### Key points
- **Standalone MCP server on ESP32**
- **Direct SSE connection** (server → client) + **HTTP POST** (client → server)
- **No proxy or intermediary software required**
- **Compatible with Alpaca devices** (OnStepNinja, NS-5000, etc.)

2026-02-3: OnStep NS-3000 Release
The customized OnStep derivative **OnStep NS-3000**  
(ESP32 firmware binaries and source code) is now available for free on BOOTH.
This repository does not include the firmware itself.  
Please refer to the BOOTH page for binaries, source code, and schematics.
https://onstepninja.booth.pm/items/7944168
Note: OnStep NS-3000 is an independent derivative and is not an official OnStep release.

2026-02-1: New Project: AiMCPserver - MCP Protocol Support for AI Agent Control
Due to browser security policy changes in late 2025, AI agents (Comet Browser, etc.) can no longer directly access local network devices (192.168.x.x). This has disabled AI-powered telescope control via AiBridge.
Solution: We are developing AiMCPserver, an ESP32-based server implementing the Model Context Protocol (MCP) - the new standard for AI agent integration.
Key Features:
📱 MCP Protocol - Claude Desktop, Antigravity support
🔭 Alpaca Protocol - NINA, PHD2, astronomy software support
📡 LX200 Protocol - SkySafari, legacy software support
🔍 Alpaca Discovery - Automatic network device detection
⚡ ESP32-based - Low cost, low power consumption

2026-01-24: The official AiBridge-compatible OnStepNinja GoTo system repository is being prepared for public release: https://github.com/OnStepNinja/OnStepNinja

2026-01-16: ASCOM Remote Server Integration Proof
Added two key assets to /assets/:
AiBridge_System_Concept_2026-1-15.jpg: Complete system diagram illustrating AiBridge's role in connecting AiBrowser, Alt-Az/Weather sensors, ASCOM Drivers, and Remote Server stack for automated observatory control.
AiBridge-to-ASCOM-Remote-Server-Integration.png: Live screenshot showing ASCOM/Alpaca telescope discovery in action, confirming AiBridge successfully detects and interfaces with simulators, OnStepNinja mounts, and ASCOM-compliant devices.​
Development Note: AiSolverCam (imaging + plate solving), AiCamera, and AiFocuser are under active development for full integration. Your feedback on this ASCOM Remote Server connection verification is welcome to refine these workflows!
Preparing further firmware and schematic details for OnStepNinja (OnStep+NS-5000) toward public release. Check the shared NotebookLM for detailed project notes and updates.
https://notebooklm.google.com/notebook/ea637833-f81a-4391-8401-2dc56ee49a69


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

