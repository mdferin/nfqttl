# NFQTTL v3.2 - Magisk Module

**Version:** 3.2-cikgusuraya  
**Original Developer:** cyborg-one  
**Current Developer:** cikgusuraya  
**License:** GPL-3.0

---

## 👥 Developer Credits

| Role | Developer | Contributions |
|------|-----------|---------------|
| **Original Developer** | cyborg-one | Created original NFQTTL - Core TTL bypass functionality, NFQueue integration, binary development |
| **Current Developer** | cikgusuraya | Forked & enhanced v3.2 - Added WiFi TTL preservation, hotspot fix improvements, mobile data optimization, documentation updates |

---

## 📋 Description

**Nfqttl v3.2: Mobile Data TTL Bypass + Hotspot Fix. WiFi keeps original TTL. No buffering!**

This Magisk module bypasses carrier-imposed **TTL (Time To Live) restrictions** on mobile hotspot/tethering, allowing unlimited hotspot usage without detection.

---

## ✨ Features

| Feature | Status |
|---------|--------|
| Mobile Data TTL Bypass | ✅ |
| Hotspot/Tethering Fix | ✅ |
| WiFi Keeps Original TTL | ✅ |
| No Buffering/Lag | ✅ |
| IPv4 + IPv6 Support | ✅ |
| Auto-Restart on Crash | ✅ |

**Supported Architectures:**
- `arm64-v8a` (Modern 64-bit phones)
- `armeabi-v7a` (Older 32-bit phones)
- `x86` (Emulators/Intel tablets)
- `x86_64` (64-bit Intel devices)

---

## 📥 Installation

1. Ensure your device is **rooted with Magisk** installed
2. Download the module ZIP file
3. Flash via **Magisk Manager** or custom recovery (TWRP, etc.)
4. **Reboot** your device

---

## ⚙️ How It Works

### The Problem
Carriers detect hotspot usage by monitoring TTL values:
- Phone browsing: **TTL = 64**
- Laptop/Device via hotspot: **TTL = 63** (decremented by 1)
- Carrier detects difference → blocks/throttles hotspot

### The Solution

```
┌─────────────────────────────────────────────────────────┐
│  nfqttl binary (daemon mode, TTL=64)                    │
│         ↓                                               │
│  iptables -t mangle                                     │
│         ↓                                               │
│  NFQUEUE (queue-num 6464)                               │
│         ↓                                               │
│  All packets intercepted → TTL modified → Forwarded     │
└─────────────────────────────────────────────────────────┘
```

**Process Flow:**
1. Module loads `nfqttl` binary at boot
2. Creates iptables chains (`nfqttli`, `nfqttlo`) for input/output
3. All network packets queued to `nfqttl` via `NFQUEUE`
4. Binary modifies TTL value to **64** for mobile data
5. WiFi traffic remains untouched (original TTL from router)

---

## 🔧 Technical Details

### Files Overview

| File | Purpose |
|------|---------|
| `customize.sh` | Installation validation, binary setup, initial iptables config |
| `service.sh` | Boot-time service, iptables rules setup, auto-restart logic |
| `nfqttl` | Core binary (ELF executable) - handles TTL modification |
| `module.prop` | Module metadata |

### iptables Rules Created

**IPv4:**
```bash
iptables -t mangle -N nfqttli
iptables -t mangle -A nfqttli -j NFQUEUE --queue-num 6464
iptables -t mangle -A PREROUTING -j nfqttli
iptables -t mangle -A OUTPUT -j nfqttlo
```

**IPv6:**
```bash
ip6tables -t mangle -N nfqttli
ip6tables -t mangle -A nfqttli -j NFQUEUE --queue-num 6464
ip6tables -t mangle -A PREROUTING -j nfqttli
ip6tables -t mangle -A POSTROUTING -j nfqttlo
```

### Default Configuration

| Parameter | Value |
|-----------|-------|
| TTL Value | 64 |
| Queue Number | 6464 |
| Daemon Mode | Enabled |
| Restart Delay | 5 seconds |
| Max Restart Attempts | 8 |

---

## ⚠️ Important Notes

1. **NOT a DNS bypass** - This module only modifies TTL values, no DNS manipulation
2. **Binary is closed-source** - The `nfqttl` binary is a compiled ELF executable
3. **WiFi unaffected** - Only mobile data packets are modified
4. **Carrier-specific** - May not work with all carriers (depends on their detection method)

---

## 🐛 Troubleshooting

| Issue | Solution |
|-------|----------|
| Module won't install | Check if binary exists for your CPU architecture |
| Hotspot still blocked | Carrier may use other detection methods |
| No internet after install | Reboot device, check iptables rules |
| Module bootloop | Flash in recovery, uninstall module |

---

## 📞 Support

For issues, questions, or feature requests, contact: **cikgusuraya**

---

## 📜 License

This project is licensed under the **GNU General Public License v3.0 (GPL-3.0)**.

See the [LICENSE](LICENSE) file for full terms and conditions.

---

## ⚖️ Disclaimer

- Use at your own risk
- May violate carrier terms of service
- Author not responsible for any damages or issues
- Ensure compliance with local laws and regulations
