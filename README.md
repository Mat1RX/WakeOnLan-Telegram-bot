# 🚀 High-Performance WOL Telegram Bot (Rust)

A lightweight, secure, and extremely resource-efficient Telegram bot designed to wake computers via **Wake-on-LAN (WOL)**. It is specifically optimized for **routers** (Keenetic, ASUS, OpenWRT) and SBCs with limited RAM and CPU.

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
# List of authorized Telegram User IDs
allowed_users = [123456789, 987654321]

[devices]
# "Alias" = ["MAC_ADDRESS", "IP_ADDRESS"]
"gaming-pc" = ["AA:BB:CC:DD:EE:FF", "192.168.1.50"]
"nas" = ["11:22:33:44:55:66", "192.168.1.100"]

```

### 3. Environment Variable

The bot requires your Telegram Token. Set it as an environment variable:

```bash
export TELOXIDE_TOKEN=your_bot_token_here

```

---

## 🤖 Usage

Once the bot is running, interact with it via Telegram:

| Command | Description |
| --- | --- |
| `/list` | List all configured devices and their aliases. |
| `/wake <alias>` | Send Magic Packet and check status after 30s. |
| `/status <alias>` | Instantly ping the device to check if it's UP. |
| `/help` | Show available commands. |

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

