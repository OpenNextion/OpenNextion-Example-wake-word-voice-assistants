# OpenNextion ESPHome voice assistant examples

This repository provides ESPHome wake-word voice assistant configurations for OpenNextion ESP32-S3 development boards.

The configurations turn supported OpenNextion boards into Home Assistant Assist satellites with local wake-word detection, microphone input, speaker playback, touchscreen status UI, Wi-Fi provisioning, and timer alarm handling.

The boards expose microphone and speaker interfaces, but the microphone and speaker modules are not directly included with the board. The board reference links include OpenNextion microphone and speaker module information and purchase links as optional accessory references; other compatible microphone and speaker hardware can also be used.

## Features

- Home Assistant Assist voice satellite support through ESPHome's native API.
- On-device wake word detection with `micro_wake_word`.
- PDM microphone capture for speech-to-text pipelines.
- I2S speaker output for Home Assistant text-to-speech responses.
- Touchscreen UI for listening, thinking, replying, error, mute, and timer states.
- Touch-to-stop behavior for Home Assistant Assist timer alarms.
- ESPHome fallback AP and captive portal Wi-Fi provisioning.
- Device Builder-friendly YAML layout with per-board documentation.

## OpenNextion board support

This branch includes configurations for the following OpenNextion ESP32-S3 boards:

| Model | Display | Configuration | Board reference | Status |
| :--- | :--- | :--- | :--- | :--- |
| ONX2432G028 | 2.8-inch ST7789 | `OpenNextion/ONX2432G028/onx2432g028.yaml` | [OpenNextion-SKU-ONX2432G028](https://github.com/OpenNextion/OpenNextion-SKU-ONX2432G028) | Display, touch, PDM microphone, I2S speaker, Home Assistant Assist flow, and touch-to-stop timer alarms validated |
| ONX3248G035 | 3.5-inch ST7796U | `OpenNextion/ONX3248G035/onx3248g035.yaml` | [OpenNextion-SKU-ONX3248G035](https://github.com/OpenNextion/OpenNextion-SKU-ONX3248G035) | Display, touch, PDM microphone, I2S speaker, Home Assistant Assist flow, and touch-to-stop timer alarms validated |

Each board directory contains its own README with hardware notes, pin mapping, Wi-Fi provisioning, Home Assistant discovery, Assist setup, and ESPHome Device Builder sync guidance.

## Demo

Click the preview image to watch the demo video.

[![OpenNextion ESPHome voice assistant demo](https://github.com/OpenNextion/OpenNextion-Example-wake-word-voice-assistants/releases/download/screenshot/video_screenshot1.jpg)](https://github.com/OpenNextion/OpenNextion-Example-wake-word-voice-assistants/releases/tag/demo-assets)

## Getting started

1. Choose the YAML file for your board.
2. Copy the board YAML file itself into the root of your ESPHome Device Builder configuration directory.
3. Build and install the firmware with ESPHome.
4. Use the fallback ESPHome access point and captive portal to provision Wi-Fi if needed.
5. Add the ESPHome device in Home Assistant and select an Assist voice pipeline.

Do not copy the whole board directory into the ESPHome configuration directory if you want the Device Builder web UI to show a device card. Copy the main YAML file to the ESPHome configuration root instead.

For board-specific commands and setup details, see:

- `OpenNextion/ONX2432G028/README.md`
- `OpenNextion/ONX3248G035/README.md`
