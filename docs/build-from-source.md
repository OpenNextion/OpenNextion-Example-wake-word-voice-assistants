# Build from Source

This document is for users who want to build or customize the OpenNextion
ESPHome Voice Assistant firmware from source instead of using release binaries.

The main README is focused on normal installation and Home Assistant setup. Keep
the detailed ESPHome build, upload, and log commands here so the project landing
page stays easier to read.

## ESPHome Device Builder Layout

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

## Validate Configuration

```bash
esphome config OpenNextion/ONX2432G028/onx2432g028.yaml
esphome config OpenNextion/ONX3248G035/onx3248g035.yaml
```

## Build

```bash
esphome compile OpenNextion/ONX2432G028/onx2432g028.yaml
esphome compile OpenNextion/ONX3248G035/onx3248g035.yaml
```

## Flash and View Logs

Replace the serial device as needed for your system:

```bash
esphome run OpenNextion/ONX2432G028/onx2432g028.yaml --device /dev/ttyUSB0
esphome run OpenNextion/ONX3248G035/onx3248g035.yaml --device /dev/ttyUSB0
```

## View Logs Without Reflashing

Use `esphome logs` when you only want to view serial logs and do not want to
compile or upload firmware again.

```bash
esphome logs OpenNextion/ONX2432G028/onx2432g028.yaml --device /dev/ttyUSB0
esphome logs OpenNextion/ONX3248G035/onx3248g035.yaml --device /dev/ttyUSB0
```

## ESPHome Factory Binary

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
