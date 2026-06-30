# ESPHome firmwares

This repo holds the source of various firmwares used for installing ESPHome onto devices with [esphome/esp-web-tools](https://github.com/esphome/esp-web-tools).

## OpenNextion board support

This branch includes ESPHome wake-word voice assistant configurations for the following OpenNextion ESP32-S3 boards:

| Model | Display | Configuration | Board reference | Status |
| :--- | :--- | :--- | :--- | :--- |
| ONX2432G028 | 2.8-inch ST7789 | `OpenNextion/ONX2432G028/onx2432g028.yaml` | [OpenNextion-SKU-ONX2432G028](https://github.com/OpenNextion/OpenNextion-SKU-ONX2432G028) | Display, touch, PDM microphone, I2S speaker, Home Assistant Assist flow, and touch-to-stop timer alarms validated |
| ONX3248G035 | 3.5-inch ST7796U | `OpenNextion/ONX3248G035/onx3248g035.yaml` | [OpenNextion-SKU-ONX3248G035](https://github.com/OpenNextion/OpenNextion-SKU-ONX3248G035) | Display, touch, PDM microphone, I2S speaker, Home Assistant Assist flow, and touch-to-stop timer alarms validated |

Each board directory contains its own README with hardware notes, pin mapping, Wi-Fi provisioning, Home Assistant discovery, Assist setup, and ESPHome Device Builder sync guidance.
