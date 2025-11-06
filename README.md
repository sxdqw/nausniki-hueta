Markdown

# Baseus/Cheap-Bluetooth-Fix for Arch Linux (PipeWire)

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE.md)

A robust, step-by-step solution to two common problems caused by faulty firmware in inexpensive Bluetooth audio devices (often Baseus) on Linux systems using PipeWire.

---

## 💡 The Problem

Inexpensive audio devices with poor firmware (like Baseus headphones or 2.4GHz dongles) often fail on Linux where they work on Windows due to two main issues:
1.  **Connection Instability (The "2-Second Disconnect"):** The device forcibly disconnects itself (`Reason.Remote`) because it cannot properly negotiate connection parameters during boot.
2.  **Low Audio Quality ("The Toilet Sound"):** The system defaults to the low-quality **HSP/HFP** profile (headset mode) instead of high-quality **A2DP**.

## ✅ The Solution

This guide provides a two-part fix:
1.  **Hardware Reset Script:** A systemd service to automatically reset the Bluetooth driver upon boot, stabilizing the connection.
2.  **WirePlumber Config:** A Lua configuration file to permanently block the low-quality HSP/HFP profile.

---

## 🚀 Installation & Setup

### Step 1: Clone the Repository
```bash
# Install git if you don't have it
sudo pacman -S git

# Clone the repository (replace URL with yours)
git clone [https://github.com/sxdqw/nausniki-hueta.git](https://github.com/sxdqw/nausniki-hueta.git)
cd nausniki-hueta

Step 2: Install the Reset Script (nausniki-hueta)

This script forces a driver reload to resolve connection instability.

    Copy the Script and Set Permissions:
    Bash

mkdir -p ~/.local/bin
cp nausniki-hueta ~/.local/bin/
chmod +x ~/.local/bin/nausniki-hueta

Configure NOPASSWD (Run Without Password): This step prevents the system from asking for your password when running the script.
Bash

    # Add the rule to the sudoers.d file
    echo "$(whoami) ALL=(ALL) NOPASSWD: /usr/bin/modprobe -r btusb, /usr/bin/modprobe btusb, /usr/bin/systemctl restart bluetooth.service" | sudo tee /etc/sudoers.d/99-bluetooth-fix

    # Set secure permissions
    sudo chmod 440 /etc/sudoers.d/99-bluetooth-fix

Step 3: Block Headset Mode (HSP/HFP)

This prevents the "toilet sound" by forcing high-quality A2DP.

    Copy the LUA Config (56-a2dp-only.lua):
    Bash

mkdir -p ~/.config/wireplumber/bluetooth.lua.d
cp 56-a2dp-only.lua ~/.config/wireplumber/bluetooth.lua.d/

Restart PipeWire Manager:
Bash

    systemctl --user restart wireplumber.service

Step 4: Setup Autostart (Recommended)

Automate the driver reset 10 seconds after your desktop loads.

    Copy the Systemd Unit File:
    Bash

mkdir -p ~/.config/systemd/user

# !!! IMPORTANT: Verify the path in bluetooth-fix.service is correct !!!
cp bluetooth-fix.service ~/.config/systemd/user/

Enable the Service:
Bash

    systemctl --user daemon-reload
    systemctl --user enable bluetooth-fix.service

Step 5: Final Step

Reboot your computer. The fix is now permanent.
Bash

reboot

📄 License

This project is licensed under the MIT License. See the LICENSE.md file for details.
