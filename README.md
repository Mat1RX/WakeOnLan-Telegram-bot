# 🚀 High-Performance WOL Telegram Bot (Rust)

A lightweight, secure, and extremely resource-efficient Telegram bot designed to wake computers via **Wake-on-LAN (WOL)**. It is specifically optimized for **routers** (Keenetic, ASUS, OpenWRT) and SBCs with limited RAM and CPU.

https://deepwiki.com/Mat1RX/WakeOnLan-Telergam-bot

## ✨ Features

* **Ultra-low Footprint**: Binary size is **~1MB** (UPX compressed) and uses **< 5MB of RAM**.
* **Static Binaries**: Compiled against `musl` for zero dependencies on the host system.
* **Security First**: Strictly whitelists users by their Telegram ID.
* **Smart Confirmation**: Automatically pings the target device 30 seconds after waking to confirm it's online.
* **Multi-Arch**: Pre-compiled for `x86_64`, `ARMv7`, `AArch64`, and `MIPS` (Big & Little Endian).

---

## 🛠 Installation

### 1. Download

Download the pre-compiled binary for your architecture from the **[Releases](https://github.com/Mat1RX/WakeOnLan-Telergam-bot/releases/tag/v0.1.0)** page.

* **Keenetic / MTK Routers**: Use `mipsel-unknown-linux-musl`.
* **Older TP-Link / Atheros**: Use `mips-unknown-linux-musl`.
* **Modern ASUS / Raspberry Pi**: Use `armv7` or `aarch64`.

### 2. Configuration

Create a `config.toml` in the same folder as the binary:

```toml
# --- WOL Bot Configuration ---

# List of Telegram User IDs allowed to control the bot.
# You can get your ID from bots like @userinfobot
allowed_users = [
    123456789, 
    987654321
]

# The network interface to use for broadcasting Magic Packets.
# On OpenWrt, 'br-lan' is the standard bridge for the local network.
interface = "br-lan"

# Devices mapping: "unique_name" = ["MAC_ADDRESS", "IP_ADDRESS", "TIMEOUT_SECS"]
# - MAC is used for waking up the device (WOL).
# - IP is used for checking the status (Ping).
# - TIMEOUT_SECS is used for ping timeout after wake
[devices]
gaming_pc = ["AA:BB:CC:DD:EE:FF", "192.168.1.10", "30"]
home_server = ["11:22:33:44:55:66", "192.168.1.20", "60"]
nas = ["00:11:22:33:44:55", "192.168.1.30", "90"]

```

### 3. Environment Variable

The bot requires your Telegram Token. Set it as an environment variable:

```bash
export TELOXIDE_TOKEN=your_bot_token_here

```

---

## 🤖 Usage

Once the bot is running, interact with it via Telegram:

 **Command**    | **Description**                                                              
----------------|------------------------------------------------------------------------------
 /start         | Displays the welcome message and help menu.                                  
 /list          | Lists all devices configured in config.toml.                                 
 /status <name> | Pings a specific device to check if it is reachable.                         
 /status_all    | Pings all configured devices and returns a report.                           
 /wake <name>   | Sends the Magic Packet, waits for the configured timeout, and checks status. 



---

## 🏗 Deployment on Routers

### Using Systemd (OpenWRT / Linux)

Create `/etc/systemd/system/wolbot.service`:

```ini
[Unit]
Description=WOL Telegram Bot
After=network.target

[Service]
Type=simple
WorkingDirectory=/opt/wolbot
ExecStart=/opt/wolbot/router_wol_bot
Environment=TELOXIDE_TOKEN=your_token_here
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target

```

### Monitoring Logs

The bot logs to `stdout` for minimal overhead. View logs with:

```bash
journalctl -u wolbot -f

```

---

## 🔨 Build from Source

If you want to build it manually with maximum optimization:

1. Install **Rust Nightly**.
2. Install `cross` for cross-compilation: `cargo install cross`.
3. Build using the nightly `build-std` flag for minimum size:

```bash
cross build --release --target [your-target] -Z build-std=std,panic_abort

```

4. Compress the output binary:

```bash
upx -9 target/[target]/release/router_wol_bot

```

---

## 📄 License

This project is licensed under the **GPL-3.0**.

