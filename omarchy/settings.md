

I spent an entire week fighting my Wi-Fi and Bluetooth with nothing but config files… don’t worry, you won’t have to rage-quit like I did!


## 1️⃣ Install Required Packages

Open a terminal and run:

```bash
sudo pacman -S \
    rofi \
    networkmanager \
    nm-connection-editor \
    pavucontrol \
    blueman \
    wdisplays \
    nwg-look \
    tlpui \
    polkit-kde-agent
```

### Notes:

- `rofi` → Menu launcher for selecting settings.
    
- `nm-connection-editor` → GUI to manage Wi-Fi / static IP.
    
- `pavucontrol` → GUI for audio settings.
    
- `blueman` → Bluetooth manager.
    
- `wdisplays` → GUI display manager.
    
- `nwg-look` → Appearance / themes GUI.
    
- `tlpui` → Power management GUI.
    
- `polkit-kde-agent` → Allows apps like network manager to request permissions.
    

---

## 2️⃣ Backup your current config (Highly recommended)

```bash
mkdir -p ~/backups
tar -czvf ~/backups/omarchy-dotfiles-$(date +%Y-%m-%d).tar.gz \
    ~/.config ~/.local ~/.bashrc ~/.profile
```

- This ensures you can restore your previous setup if something goes wrong.
    

---

## 3️⃣ Verify NetworkManager and Polkit

Start and enable NetworkManager:

```bash
sudo systemctl enable --now NetworkManager
```

Check that polkit agent is available:

```bash
/usr/lib/polkit-kde-authentication-agent-1 &
```

> This must run for GUI apps that need permissions (Wi-Fi, Bluetooth).

---

## 4️⃣ Create the “Settings” Script

Create a new script:

```bash
mkdir -p ~/.local/bin
nano ~/.local/bin/settings
```

Paste the following content:

```bash
#!/bin/bash

choice=$(printf "Network\nAudio\nBluetooth\nDisplay\nAppearance\nPower" | rofi -dmenu -i -p "System Settings")

case "$choice" in
  Network) nm-connection-editor ;;
  Audio) pavucontrol ;;
  Bluetooth) blueman-manager ;;
  Display) wdisplays ;;
  Appearance) nwg-look ;;
  Power) tlpui ;;
esac
```

Make it executable:

```bash
chmod +x ~/.local/bin/settings
```

---

## 5️⃣ Add `~/.local/bin` to your PATH

Check if `~/.local/bin` is in PATH:

```bash
echo $PATH
```

If it’s missing, add it:

```bash
nano ~/.bashrc
```

Add:

```bash
export PATH="$HOME/.local/bin:$PATH"
```

Reload shell:

```bash
source ~/.bashrc
```

---

## 6️⃣ Autostart Required GUI Agents

Edit your Hyprland config:

```bash
nano ~/.config/hypr/hyprland.conf
```

Add these lines:

```conf
# Polkit agent for permissions
exec-once = /usr/lib/polkit-kde-authentication-agent-1

# Network tray icon
exec-once = nm-applet

# Bluetooth tray icon
exec-once = blueman-applet
```

> This ensures your tray icons are available on login.

Reload Hyprland:

```bash
hyprctl reload
```

---

## 7️⃣ Bind the “Settings” Script to a Key

In `hyprland.conf`, add:

```conf
bind = SUPER, S, exec, settings
```

Reload Hyprland:

```bash
hyprctl reload
```

Now pressing **SUPER + S** opens your **System Settings menu**.

---

## 8️⃣ Test the Setup

1. Press **SUPER + S** → a menu should pop up.
    
2. Select **Network** → `nm-connection-editor` opens.
    
3. Select **Audio** → `pavucontrol` opens.
    
4. Bluetooth, Display, Appearance, and Power should all open their respective GUIs.
    

---

## 9️⃣ Optional Enhancements

- **Waybar button:** Add a “Settings” button on Waybar that runs `settings`.
    
- **Replace Rofi with Wofi:** For Wayland-native launcher integration.
    
- **Custom icons / themes:** Use `nwg-look` to match Omarchy theme.
    

---

## ✅ Result

You now have:

- GUI Wi-Fi (with static IP)
    
- GUI Bluetooth
    
- GUI Audio controls
    
- GUI Display and Appearance controls
    
- Power management GUI
    
- Single shortcut (**SUPER+S**) to access everything
    
- Tray integration for Wi-Fi, Bluetooth, and Polkit
    

All without touching Omarchy’s Hyprland workflow or keybindings.
