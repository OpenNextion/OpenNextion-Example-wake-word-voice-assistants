# OpenNextion ESPHome Voice Assistant

[![English](https://img.shields.io/badge/lang-English-blue)](./README.md)
[![中文](https://img.shields.io/badge/lang-中文-red)](./README.zh-CN.md)

<p align="center">
  <img src="docs/images/opennextion-esphome-voice-assistant-demo.jpg" alt="OpenNextion ESPHome voice assistant demo on OpenNextion display" width="820">
</p>

OpenNextion ESPHome Voice Assistant is an OpenNextion board support fork of
[ESPHome wake-word voice assistants](https://github.com/esphome/wake-word-voice-assistants).
It adds ready-to-build ESPHome configurations for OpenNextion ESP32-S3 display
boards and turns them into Home Assistant Assist voice satellites.

This repository is intended to make the ESPHome wake-word voice assistant
examples easier to build, flash, and validate on supported OpenNextion
development boards while the upstream board support pull requests are under
review.

## Supported Boards

The current public branch focuses on two OpenNextion ESP32-S3 display boards:

| ESPHome YAML | Display model | Size | Resolution | Display driver | Status |
| --- | --- | --- | --- | --- | --- |
| `OpenNextion/ONX3248G035/onx3248g035.yaml` | [ONX3248G035][onx3248g035] | 3.5 inch | 320 x 480 | ST7796U | Verified |
| `OpenNextion/ONX2432G028/onx2432g028.yaml` | [ONX2432G028][onx2432g028] | 2.8 inch | 240 x 320 | ST7789 | Verified |

Each board directory contains its own README with hardware notes, pin mapping,
Wi-Fi provisioning, Home Assistant discovery, Assist setup, and ESPHome Device
Builder sync guidance.

The boards expose microphone and speaker interfaces, but microphone and speaker
modules are not directly included with the boards. The board reference links
include OpenNextion microphone and speaker module information and purchase links
as optional accessory references; other compatible microphone and speaker
hardware can also be used.

Do not flash firmware built for one display model onto another display model.

## Background

I wanted to adapt the OpenNextion development boards I had on hand into Home
Assistant voice assistant input and output devices. The official Home Assistant
documentation introduced the ESPHome wake-word voice assistant project, so I
forked that project and added support for my OpenNextion boards.

OpenNextion boards are a good fit for this kind of DIY Home Assistant voice
assistant project because they combine ESP32-S3, an SPI TFT display, capacitive
touch, microphone input, speaker output, and published hardware reference files
on a compact development board. This makes it easier to build an interactive
voice satellite with both visual feedback and touch control, instead of wiring a
display, touch panel, audio input, and audio output from scratch.

This fork keeps the original ESPHome voice assistant behavior and adds the
OpenNextion-specific board configuration needed by the supported boards.

## 3D Printed Enclosure

I also designed simple 3D printed enclosures for the supported OpenNextion
display sizes and will publish them on MakerWorld. Anyone who needs them can
download and print them for free.

Each enclosure should match the corresponding OpenNextion display size and leave
space for the microphone and speaker wiring used by the voice assistant setup.

MakerWorld project links:

- 3.5 inch ONX3248G035 enclosure: link to be added
- 2.8 inch ONX2432G028 enclosure: link to be added

## Current Porting Work

This version is based on ESPHome wake-word voice assistants and adds
OpenNextion multi-board support. The main changes are:

### 1. OpenNextion Board Support

OpenNextion ESPHome Voice Assistant includes dedicated ESPHome YAML
configurations for:

- [ONX3248G035][onx3248g035] 3.5 inch display
- [ONX2432G028][onx2432g028] 2.8 inch display

The OpenNextion configurations are grouped under `OpenNextion/` to avoid adding
one top-level directory per board model as the series grows.

### 2. Display and Board Initialization

The port adds the OpenNextion display initialization required by the supported
boards:

- ST7796U SPI TFT panel support for ONX3248G035
- ST7789 SPI TFT panel support for ONX2432G028
- BGR color order for the supported panels
- PCF8574 IO expander support for LCD reset and speaker amplifier control
- Backlight GPIO setup
- CST826 capacitive touch support on the shared I2C bus

### 3. Voice Assistant Hardware

The OpenNextion configurations expose the hardware needed by Home Assistant
Assist:

- PDM microphone input on GPIO19 / GPIO20
- I2S speaker output on GPIO16 / GPIO14 / GPIO15
- PCF8574-controlled speaker amplifier enable
- ESPHome speaker media player for text-to-speech playback
- ESPHome voice assistant component using the configured microphone and speaker

### 4. Wake Word, UI, and Timer Behavior

The port keeps the original ESPHome voice assistant UI flow and adapts it to
the OpenNextion displays:

- On-device wake word detection with `micro_wake_word`
- Default wake word: Okay Nabu
- Listening, thinking, replying, error, mute, and timer-finished display states
- Touch-to-stop behavior for Home Assistant Assist timer alarms
- ESPHome fallback AP and captive portal Wi-Fi provisioning

## Current Validation Status

### Display Validation

<p align="center">
  <img src="docs/images/opennextion-esphome-voice-assistant-validation.jpg" alt="OpenNextion ESPHome voice assistant validation on OpenNextion display" width="720">
</p>

- ONX3248G035 has been validated on real hardware
- ONX2432G028 has been validated on real hardware
- Display output has been validated on both OpenNextion boards
- CST826 touch input has been validated on both OpenNextion boards
- PDM microphone input has been validated with Home Assistant Assist
- I2S speaker output has been validated with Home Assistant TTS responses
- Local wake word detection has been validated with the configured models
- Assist timers can ring on the device and be stopped by touching the screen

### Firmware Validation Matrix

Legend: ✅ Verified / ⚠️ Hardware-dependent / ⏳ Not used

| Board | Build | Boot | Display | Touch | Microphone | Speaker | Assist flow | Timer alarm | Notes |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| ONX3248G035 | ✅ Verified | ✅ Verified | ✅ Verified | ✅ Verified | ✅ Verified | ✅ Verified | ✅ Verified | ✅ Verified | 3.5 inch ST7796U display |
| ONX2432G028 | ✅ Verified | ✅ Verified | ✅ Verified | ✅ Verified | ✅ Verified | ✅ Verified | ✅ Verified | ✅ Verified | 2.8 inch ST7789 display |

## ESPHome Device Builder Usage

ESPHome Device Builder shows device cards for YAML files placed directly in its
configuration root. Keep the source files in this repository under
`OpenNextion/`, but copy the main board YAML file itself to the ESPHome
configuration root.

Example:

```bash
cp OpenNextion/ONX2432G028/onx2432g028.yaml /path/to/esphome/config/onx2432g028.yaml
cp OpenNextion/ONX3248G035/onx3248g035.yaml /path/to/esphome/config/onx3248g035.yaml
```

Do not copy the whole `OpenNextion/ONX2432G028/` or
`OpenNextion/ONX3248G035/` directory into the ESPHome configuration directory.
Command-line ESPHome can compile nested YAML paths, but the Device Builder web
UI will not show a device card unless the YAML file is in the configuration
root.

## Wi-Fi Provisioning and Home Assistant Setup

The OpenNextion configurations enable ESPHome's fallback access point and
captive portal. If the device cannot connect to a known Wi-Fi network after
boot, it starts its own temporary Wi-Fi access point.

1. Flash the matching firmware for your board.
2. Connect to the fallback ESPHome access point for the device.
3. Open `http://192.168.4.1/` if the captive portal does not open
   automatically.
4. Select your 2.4 GHz Wi-Fi network and enter the password.
5. Add the ESPHome device in Home Assistant.
6. Configure Home Assistant Assist and select a voice pipeline.

Use a stable 2.4 GHz network for voice assistant use. Weak signal or roaming
scans can delay TTS streaming and make playback choppy.

## Firmware Download and Flashing

Download firmware from the GitHub Release page when release binaries are
available. ESPHome factory binaries are intended for full initial flashing from
address `0x0`.

Release files can follow this naming pattern:

```text
opennextion-esphome-voice-assistant-<version>-<target>.factory.bin
```

| Target | Example firmware file | Flash address |
| --- | --- | --- |
| [onx3248g035][release-downloads] | `opennextion-esphome-voice-assistant-v0.1.0-onx3248g035.factory.bin` | `0x0` |
| [onx2432g028][release-downloads] | `opennextion-esphome-voice-assistant-v0.1.0-onx2432g028.factory.bin` | `0x0` |

Flash a factory binary with:

```bash
VERSION=v0.1.0
python -m esptool --chip esp32s3 -p /dev/cu.wchusbserial1110 -b 921600 write_flash \
  0x0 ./opennextion-esphome-voice-assistant-${VERSION}-onx2432g028.factory.bin
```

Replace `VERSION`, serial port, and firmware target name as needed for your
board.

For this project, full firmware flashing is recommended for first installation.
OTA firmware downloads are not provided unless the OTA flow is separately
validated.

## Local Build, Flash, and Logs

This project uses ESPHome with the ESP-IDF framework. The commands below are
for OpenNextion boards.

### Validate Configuration

```bash
esphome config OpenNextion/ONX2432G028/onx2432g028.yaml
esphome config OpenNextion/ONX3248G035/onx3248g035.yaml
```

### Build

```bash
esphome compile OpenNextion/ONX2432G028/onx2432g028.yaml
esphome compile OpenNextion/ONX3248G035/onx3248g035.yaml
```

### Flash and View Logs

Replace the serial device as needed for your system:

```bash
esphome run OpenNextion/ONX2432G028/onx2432g028.yaml --device /dev/ttyUSB0
esphome run OpenNextion/ONX3248G035/onx3248g035.yaml --device /dev/ttyUSB0
```

## Single Factory Binary

ESPHome generates a single `firmware.factory.bin` file that can be written from
address `0x0`. This is useful for full initial flashing with `esptool`.

When compiling directly from this repository, the files are generated under the
YAML directory:

```text
OpenNextion/ONX2432G028/.esphome/build/onx2432g028/.pioenvs/onx2432g028/firmware.factory.bin
OpenNextion/ONX3248G035/.esphome/build/onx3248g035/.pioenvs/onx3248g035/firmware.factory.bin
```

When compiling from an ESPHome Device Builder configuration root, the files are
generated under that configuration directory:

```text
.esphome/build/onx2432g028/.pioenvs/onx2432g028/firmware.factory.bin
.esphome/build/onx3248g035/.pioenvs/onx3248g035/firmware.factory.bin
```

Flash a factory binary with:

```bash
python -m esptool --chip esp32s3 -p /dev/cu.wchusbserial1110 -b 921600 write_flash \
  0x0 .esphome/build/onx2432g028/.pioenvs/onx2432g028/firmware.factory.bin
```

Replace the serial port and firmware path as needed for your board.

## Roadmap

Planned next steps:

- Keep this OpenNextion fork aligned with upstream ESPHome wake-word voice assistants where practical
- Continue tracking the upstream OpenNextion board support pull requests
- Add more photos and validation images for the supported boards
- Publish convenient release firmware binaries when the release flow is ready
- Continue adding support for more OpenNextion ESP32-S3 display boards

## Credits

This project is based on ESPHome wake-word voice assistants. Thanks to the
original project and the related open source projects.

- ESPHome wake-word voice assistants: https://github.com/esphome/wake-word-voice-assistants
- Home Assistant: https://www.home-assistant.io/
- ESPHome: https://esphome.io/
- OpenNextion open source projects: https://github.com/OpenNextion
- OpenNextion board documentation: https://nextion.tech/wiki/

## License

This project preserves the upstream ESPHome wake-word voice assistants license
terms. See `LICENSE` for details. Third-party libraries, ESPHome components, and
Home Assistant integrations may have their own license notices.

## Disclaimer

This project is not an official Home Assistant, ESPHome, or OpenNextion product.

Flashing and using third-party firmware involves risk. Please make sure the
selected firmware matches your exact OpenNextion board model before flashing.
This project is not responsible for device damage, data loss, network connection
issues, Home Assistant configuration issues, voice assistant behavior, or any
other consequences of use.

[onx2432g028]: https://github.com/OpenNextion/OpenNextion-SKU-ONX2432G028
[onx3248g035]: https://github.com/OpenNextion/OpenNextion-SKU-ONX3248G035
[release-downloads]: https://github.com/OpenNextion/OpenNextion-Example-wake-word-voice-assistants/releases
