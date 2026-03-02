# ⌨️ Omarchy / Hyprland Custom Keybindings Documentation

This document describes the custom `bindd` keybindings used in an
Omarchy setup (Hyprland-based environment).

------------------------------------------------------------------------

# 🔹 Application Bindings

## 🖥 Terminal

**SUPER + RETURN**\
Launches terminal in the current working directory:

``` bash
uwsm-app -- xdg-terminal-exec --dir="$(omarchy-cmd-terminal-cwd)"
```

------------------------------------------------------------------------

## 📁 File Manager

**SUPER + SHIFT + F**\
Opens Nautilus in a new window.

------------------------------------------------------------------------

## 🌐 Browser

**SUPER + SHIFT + B** → Default browser\
**SUPER + SHIFT + ALT + B** → Private browser mode

------------------------------------------------------------------------

## 🎵 Music

**SUPER + SHIFT + M**\
Launches or focuses Spotify.

------------------------------------------------------------------------

## ✍️ Editor

**SUPER + SHIFT + N**\
Launches configured editor.

------------------------------------------------------------------------

## 📊 Activity Monitor

**SUPER + SHIFT + T**\
Launches `btop` inside a TUI environment.

------------------------------------------------------------------------

## 🐳 Docker

**SUPER + SHIFT + D**\
Launches `lazydocker`.

------------------------------------------------------------------------

## 💬 Signal

**SUPER + SHIFT + G**\
Launches or focuses Signal Desktop.

------------------------------------------------------------------------

## 🧠 Obsidian

**SUPER + SHIFT + O**\
Launches or focuses Obsidian (Wayland optimized).

------------------------------------------------------------------------

## 📝 Typora

**SUPER + SHIFT + W**\
Launches Typora with Wayland IME support.

------------------------------------------------------------------------

## 🔐 Password Manager

**SUPER + SHIFT + SLASH**\
Launches 1Password.

------------------------------------------------------------------------

# 🔹 Web Applications

> If your URL contains `#`, type it as `##` to prevent Hyprland treating
> it as a comment.

  Shortcut                   Application
  -------------------------- -----------------
  SUPER + SHIFT + A          ChatGPT
  SUPER + SHIFT + ALT + A    Grok
  SUPER + SHIFT + C          Calendar
  SUPER + SHIFT + E          Email
  SUPER + SHIFT + Y          YouTube
  SUPER + SHIFT + ALT + G    WhatsApp
  SUPER + SHIFT + CTRL + G   Google Messages
  SUPER + SHIFT + P          Google Photos
  SUPER + SHIFT + X          X (Twitter)
  SUPER + SHIFT + ALT + X    X Post

All web apps are launched using `omarchy-launch-webapp` or
`omarchy-launch-or-focus-webapp`.

------------------------------------------------------------------------

# 🔹 System Controls

  Shortcut          Action
  ----------------- ------------------------
  SUPER + ALT + L   Suspend system
  SUPER + L         Lock screen (hyprlock)

------------------------------------------------------------------------

# 🔹 Screenshots & Screen Recording

Using **SUPER + GRAVE (\`)** combinations:

  Shortcut                     Action
  ---------------------------- --------------------
  SUPER + GRAVE                Screenshot region
  SUPER + SHIFT + GRAVE        Screenshot window
  SUPER + CTRL + GRAVE         Screenshot display
  SUPER + ALT + GRAVE          Record region
  SUPER + CTRL + ALT + GRAVE   Record display

Scripts used:

    ~/.local/share/omarchy/bin/omarchy-cmd-screenshot
    ~/.local/share/omarchy/bin/omarchy-cmd-screenrecord

------------------------------------------------------------------------

# 🔹 Customization Example

To overwrite an existing binding (example: Omarchy Menu on SUPER +
SPACE):

``` bash
# unbind = SUPER, SPACE
# bindd = SUPER, SPACE, Omarchy menu, exec, omarchy-menu
```

------------------------------------------------------------------------

# 📍 Location of Configuration File

These bindings are typically stored in:

    ~/.config/hypr/hyprland.conf

Or in modular configs such as:

    ~/.config/hypr/conf.d/*.conf

------------------------------------------------------------------------

# 🔄 Reload Configuration

After editing the config file:

``` bash
hyprctl reload
```

Or restart Hyprland session.
