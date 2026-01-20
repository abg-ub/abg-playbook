# Hyprland Monitor Configuration Guide

This document explains how to configure monitors in **Hyprland** (used by Omarchy) in a clear, practical way. It is written so **anyone can learn, understand, and reuse** the configurations.

---

## 1. Basics: What is a monitor config?

Hyprland uses this format:

```
monitor = <NAME>, <RESOLUTION@REFRESH>, <POSITION>, <SCALE>
```

### Example

```
monitor = HDMI-A-1, 1920x1080@60, 0x0, 1
```

### Field explanation

| Field                | Meaning                                          |
| -------------------- | ------------------------------------------------ |
| `NAME`               | Monitor name (`eDP-1`, `HDMI-A-1`, `DP-3`, etc.) |
| `RESOLUTION@REFRESH` | Screen size and refresh rate                     |
| `POSITION`           | X and Y position in the virtual layout           |
| `SCALE`              | UI scaling factor (`1`, `1.25`, `1.5`, etc.)     |

---

## 2. How to find your monitor names

Run:

```
hyprctl monitors
```

You will see output like:

```
Monitor eDP-1:
Monitor HDMI-A-1:
```

Use **these exact names** in your config.

---

## 3. Understanding positions (very important)

Hyprland uses a **2D coordinate system**:

* `0x0` → top-left corner
* X increases → to the right
* Y increases → downward

### Example layout

```
[ Monitor A ][ Monitor B ]
```

If Monitor A is `1920` pixels wide:

```
Monitor A → 0x0
Monitor B → 1920x0
```

---

## 4. Common Monitor Layouts

---

### 4.1 Laptop only

```
monitor = eDP-1, 1920x1080@60, 0x0, 1
```

---

### 4.2 External monitor only

```
monitor = HDMI-A-1, 3440x1440@60, 0x0, 1
```

---

### 4.3 External LEFT, Laptop RIGHT

```
monitor = HDMI-A-1, 3440x1440@60, 0x0, 1
monitor = eDP-1, 1920x1080@60, 3440x0, 1
```

---

### 4.4 Laptop LEFT, External RIGHT

```
monitor = eDP-1, 1920x1080@60, 0x0, 1
monitor = HDMI-A-1, 3440x1440@60, 1920x0, 1
```

---

### 4.5 External ABOVE Laptop

```
monitor = HDMI-A-1, 3440x1440@60, 0x0, 0
monitor = eDP-1, 1920x1080@60, 0x1440, 1
```

---

### 4.6 Laptop centered vertically next to Ultrawide

```
monitor = HDMI-A-1, 3440x1440@60, 0x0, 1
monitor = eDP-1, 1920x1080@60, 3440x180, 1
```

Calculation:

```
(1440 - 1080) / 2 = 180
```

---

## 5. Three-monitor setup example

```
monitor = DP-3,     1920x1080@60, 0x0, 1
monitor = HDMI-A-1, 1920x1080@60, 1920x0, 1
monitor = eDP-1,    1920x1080@60, 960x1080, 1
```

This creates:

```
[ DP-3 ][ HDMI-A-1 ]
   [   eDP-1   ]
```

---

## 6. Using scale (HiDPI setups)

### Laptop HiDPI, External normal

```
monitor = HDMI-A-1, 3440x1440@60, 0x0, 1
monitor = eDP-1, 1920x1080@60, 3440x0, 1.25
```

---

## 7. Multiple setups in one config (Office + Home)

Hyprland automatically applies layouts based on **connected monitors**.

```
# Office
monitor = DP-3, 1920x1080@60, 0x0, 1
monitor = HDMI-A-1, 1920x1080@60, 1920x0, 1
monitor = eDP-1, 1920x1080@60, 1280x1080, 1

# Home
monitor = HDMI-A-1, 3440x1440@60, 0x0, 1
monitor = eDP-1, 1920x1080@60, 3440x0, 1
```

If `DP-3` is missing, Hyprland falls back to the home setup automatically.

---

## 8. Reload config safely

```
hyprctl reload
```

---

## 9. Useful debugging commands

```
hyprctl monitors
wlr-randr
watch -n 1 hyprctl monitors
```

---

## 10. Tips & Best Practices

* Always verify monitor names
* Start layouts from `0x0`
* Calculate offsets using monitor widths/heights
* Keep office and home configs in the same file
* Comment your configs clearly

---

## 11. Reference

Official Hyprland docs:

* [https://wiki.hyprland.org](https://wiki.hyprland.org)

---


