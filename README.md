# Navratri Garba & Dandiya — on a 0.96" OLED

![Arduino](https://img.shields.io/badge/Arduino-IDE-00979D?logo=arduino&logoColor=white)
![ESP32](https://img.shields.io/badge/Board-ESP32%20%2F%20ESP8266-000000?logo=espressif&logoColor=white)
![OLED](https://img.shields.io/badge/Display-SSD1306%20128x64-111827)
![Frames](https://img.shields.io/badge/Frames-390-E11D48)
![Library](https://img.shields.io/badge/Library-Adafruit%20SSD1306-orange)
![License](https://img.shields.io/badge/code-free%20to%20use-brightgreen)

**Nine nights. One tiny screen. The circle keeps turning.**

This repo plays a **Navratri Garba and Dandiya** loop on a **0.96" SSD1306 OLED** (128×64).  
**390 PROGMEM frames. 67 ms per frame. About 15 fps. One pass ≈ 26 seconds.** Then it starts again.

Drop it on a college-fest table, a desk during Sharad Navratri, or a maker reel.  
People already know the dance. The screen just has to keep the circle moving.

**Repo:** [github.com/itzmeAshish/NavratriGarba](https://github.com/itzmeAshish/NavratriGarba)  
**Full guide:** [Navratri Garba & Dandiya OLED tutorial](https://www.oledanimationmaker.com/blog/navratri-garba-dandiya-oled-animation-arduino-ssd1306.html)  
**Made with:** [OLED Animation Maker](https://www.oledanimationmaker.com/)

---

## What you get

- 390 frames × 128×64 mono bitmaps in `PROGMEM` (`frame0` … `frame389`)
- Frame delay **67 ms** (~15 fps) — one number to change
- Full loop ≈ **26 seconds** when I2C keeps up (closer to ~35 s on a default 100 kHz bus)
- Adafruit SSD1306 + Adafruit GFX
- I2C address **0x3C**
- `uint16_t` frame index (390 does not fit in 8 bits)
- Pictures only — bring your own speaker if the table needs the dhol

---

## 60-second start

```bash
git clone https://github.com/itzmeAshish/NavratriGarba.git
```

1. Rename the folder to **`main`** (the file is `main.ino` — Arduino IDE requires the match)
2. Install **Adafruit SSD1306** and **Adafruit GFX**
3. Select **ESP32** or a **4MB ESP8266**
4. Upload
5. Watch the loop

Serial baud: **115200**.  
`SSD1306 allocation failed` means power, SDA/SCL, or try address `0x3D`.

---

## Requirements

| Item | Notes |
|------|--------|
| Board | **ESP32 or 4MB ESP8266** |
| OLED | 0.96" SSD1306 I2C 128×64 @ `0x3C` |
| IDE | Arduino IDE 1.8+ or 2.x |
| Libraries | Adafruit SSD1306, Adafruit GFX |

> **Flash:** 390 × 1024 = **399,360 bytes (390 KB)** of pictures alone.  
> Classic **Uno (32 KB)** cannot store this. **Mega 2560 (256 KB)** cannot store this either.  
> The comment inside `main.ino` mentions Uno pins because that is the generator header. Wire an ESP board.

---

## Wiring

### ESP32 (recommended)

| OLED | Board |
|------|--------|
| VCC | **3.3V** |
| GND | GND |
| SDA | **GPIO21** |
| SCL | **GPIO22** |

```cpp
Wire.begin(21, 22);
```

### ESP8266 NodeMCU / Wemos D1 Mini

| OLED | Board | GPIO |
|------|--------|------|
| VCC | **3.3V** | — |
| GND | GND | — |
| SDA | **D2** | GPIO4 |
| SCL | **D1** | GPIO5 |

```cpp
Wire.begin(4, 5);  // only if the panel stays dark
```

Set **Tools → Flash Size** to a **4MB** scheme. A 512 KB or 1 MB profile rejects this sketch even when the chip is larger.

---

## Speed

In `loop()`:

```cpp
if (millis() - lastMs >= 67) {  // lower = faster
```

| Delay | Approx FPS | Full 390-frame pass |
|------:|-----------:|--------------------:|
| 150 ms | ~6.7 | ~59 s |
| 100 ms | 10 | 39 s |
| **67 ms (stock)** | **~15** | **~26 s** |
| 40 ms | 25 | ~16 s |

If a stopwatch reads ~35 seconds at the stock 67 ms, the I2C transfer is the real clock. After the bus starts:

```cpp
Wire.setClock(400000);
```

Leave `numFrames` at **390**. Do not change the index to `uint8_t` — it wraps at 255 and cuts the loop in half.

---

## Garba and Dandiya, in one sentence each

- **Navratri** — nine nights for the goddess, celebrated in autumn (Sharad) and again in spring (Chaitra).
- **Garba** — the Gujarati circle dance around a lamp (the *garbo*).
- **Dandiya Raas** — the paired stick dance. Two sticks, a partner, a set pattern of hits.

This OLED shows the motion. It does not play audio, print aarti text, or replace a pandal screen. A 0.96" panel is a table piece: registration desk, hostel room, maker reel.

---

## How this was made

1. Open [oledanimationmaker.com](https://www.oledanimationmaker.com/)
2. Import a short clip you have the right to convert
3. Keep 128×64 and a frame count your board can store
4. **Get the Code** → Adafruit SSD1306
5. Upload

Long version, with wiring, flash math, and stall-setup notes:  
https://www.oledanimationmaker.com/blog/navratri-garba-dandiya-oled-animation-arduino-ssd1306.html

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| IDE says the sketch name does not match | Rename the folder to `main` |
| Sketch too big | ESP32 or 4MB ESP8266. Uno and Mega will not link |
| Blank screen | 3.3V, reseat SDA/SCL, try `0x3D` |
| Loop jumps backward early | `frameIdx` must stay `uint16_t` |
| Slower than 15 fps | `Wire.setClock(400000)` |
| Compile errors | Install both Adafruit libraries |

Blank-screen checklist: [oledanimationmaker.com/blog/arduino-oled-blank-screen-fix-ssd1306.html](https://www.oledanimationmaker.com/blog/arduino-oled-blank-screen-fix-ssd1306.html)

---

## Film it so the reel works

- Fill the frame with the **glass**, not the whole desk
- Kill the light behind the phone or the cover glass mirrors the room
- Record **at least 30 seconds** so one full pass completes
- A silent panel is the correct result — this sketch has no speaker

---

## Keywords

`navratri garba oled` · `dandiya oled animation` · `navratri arduino` · `garba ssd1306` · `dandiya raas oled` · `esp32 oled animation` · `esp8266 ssd1306` · `progmem bitmap` · `390 frames` · `0.96 oled` · `adafruit ssd1306`

---

## More festival & song loops

| Project | Frames | Guide |
|---------|-------:|-------|
| [Ganesha Day 7](https://github.com/itzmeAshish/GaneshaDay7) | 150 | [Blog](https://www.oledanimationmaker.com/blog/ganesha-day-7-oled-animation-arduino-ssd1306.html) |
| [Kalayani](https://github.com/itzmeAshish/kalayanionoled) | 148 | [Blog](https://www.oledanimationmaker.com/blog/kalayani-song-oled-animation-arduino-ssd1306.html) |
| [Bad Apple](https://github.com/itzmeAshish/BadApple) | 385 | [Blog](https://www.oledanimationmaker.com/blog/bad-apple-oled-animation-arduino-ssd1306.html) |
| [Thangamaana](https://www.oledanimationmaker.com/blog/thangamaana-tamil-song-oled-animation-arduino-ssd1306.html) | 200 | Tamil song loop |
| Independence Day | drawn show | [Blog](https://www.oledanimationmaker.com/blog/independence-day-oled-animation-esp8266-ssd1306.html) |

---

## Links

- **Code:** https://github.com/itzmeAshish/NavratriGarba
- **Tutorial:** https://www.oledanimationmaker.com/blog/navratri-garba-dandiya-oled-animation-arduino-ssd1306.html
- **Tool:** https://www.oledanimationmaker.com/
- **Author:** [itzmeAshish](https://github.com/itzmeAshish)
- **Instagram:** [oled_animator](https://www.instagram.com/oled_animator)
- **YouTube:** [@oled_animator](https://www.youtube.com/@oled_animator)

---

## License

Free to use for learning, demos, stalls, and personal projects.  
A star on the repo helps the next person find it during Navratri.  
Credit: link this repo and [oledanimationmaker.com](https://www.oledanimationmaker.com/).

**Jai Mata Di — and may your I2C address be 0x3C.**
