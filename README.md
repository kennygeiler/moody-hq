# Moody HQ - hair-clip display

LilyGO / HiLetgo T-Display (ESP32, 1.14" 240x135, CH9102F USB chip) that scrolls chunky
pixel text on a green background. Clipped into hair with an alligator clip.

## What it does (flashed 2026-10-09; Kenny confirmed the edit page and slides work on the device)
Plays up to 10 **slides** in a loop. A slide is text, a picture from the phone, or a QR code; each has its own
text effect (scroll, wave, rainbow, bounce, typewriter, blink, glitch), pixel animation (hearts, sparkles, rain,
snow, fire, chomper, bouncing ball, confetti - they land on / rise from / eat the text), font (7) and color.
Earlier versions are in `archive/` (text-only v1; v2 before the picture crop tool).

## Buttons
- **Right button tap:** next slide.
- **Right button hold 2 s:** edit mode. The board makes its own Wi-Fi network (name and password are shown on the
  screen; they come from `hair_display/secrets.h`, which is not in the repo - copy `secrets.example.h`). Join it from a phone; the edit
  page opens by itself (or go to `192.168.4.1`). Build slides under "Mix and match", upload up to 4 pictures
  (the preview box is the screen: drag to move, slide to zoom, Save this crop; the phone sends raw 240x135 RGB565,
  stored in LittleFS as `/img0-3.bin`), pick scroll speed,
  tap Save and play. Wi-Fi turns off after saving, a right-button tap, or 5 minutes idle.
- **Left button hold 2 s:** off (deep sleep). Right button turns it back on.

Slides live in flash (`Preferences` namespace `hair`, key `slides`: one line each,
`kind|effect|animation|font|color|picture|text`); old text-only messages are converted on first boot.
Only plain ASCII draws; iPhone smart quotes are converted, emoji are dropped.
QR code: `rm_qrcode.c/.h` is ricmoo's QRCode library copied into the sketch folder (the ESP32 core has its own
`qrcode.h`, so the library cannot be included by name).
Memory: the edit page is streamed from flash in pieces and the 64 KB picture buffer is freed while Wi-Fi is on.
Building the page as one String ran out of memory and the phone got a blank white page (2026-10-09).
If the page does not pop up on an iPhone, open Safari at `http://192.168.4.1` (turn cellular data off if it cannot connect).

## Flash it
Plug in: Mac -> Anker hub (USB-A port) -> USB-A to USB-C cable -> board.

```bash
cd ~/hair-display
arduino-cli compile --fqbn esp32:esp32:esp32 hair_display
arduino-cli upload --fqbn esp32:esp32:esp32:UploadSpeed=115200 -p /dev/cu.usbserial-59680478731 hair_display
```

- Upload at 115200: the default 921600 fails through the hub ("Invalid head of packet").
- Port name: `/bin/ls /dev | grep cu.usbserial` (the number can change per board/cable).

## Toolchain (one-time, done 2026-10-02)
- `brew install arduino-cli`
- ESP32 core **2.0.17** (`esp32:esp32@2.0.17`) - TFT_eSPI 2.5.43 has problems with core 3.x.
- `TFT_eSPI` 2.5.43, with `~/Documents/Arduino/libraries/TFT_eSPI/User_Setup_Select.h` switched
  to `User_Setups/Setup25_TTGO_T_Display.h` (original saved as `User_Setup_Select.h.orig`).

## Battery
EEMB 402535 320mAh. Its plug was wired OPPOSITE to the board. Fixed 2026-10-09: battery plug cut off, wires
twisted red-red / black-black onto the pigtail cable that came with the board, taped. Works; reads ~3.8 V.
