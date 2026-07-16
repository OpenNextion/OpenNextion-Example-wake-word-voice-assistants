# OpenNextion ESPHome Voice Assistant

[![English](https://img.shields.io/badge/lang-English-blue)](./README.md)
[![中文](https://img.shields.io/badge/lang-中文-red)](./README.zh-CN.md)

<p align="center">
  <img src="docs/images/opennextion-esphome-voice-assistant-demo.jpg" alt="OpenNextion ESPHome 语音助手在 OpenNextion 显示屏上的运行效果" width="820">
</p>

OpenNextion ESPHome Voice Assistant 是 [ESPHome wake-word voice assistants](https://github.com/esphome/wake-word-voice-assistants) 的 OpenNextion 开发板适配分支。它为 OpenNextion ESP32-S3 显示屏开发板增加了可直接构建的 ESPHome 配置，并将这些开发板变成 Home Assistant Assist 语音卫星设备。

这个仓库的目标，是让 ESPHome wake-word voice assistant 示例可以更容易地在已支持的 OpenNextion 开发板上构建、刷写和验证，同时等待上游板级支持 PR 审核。

## 支持的开发板

当前公开分支重点支持两款 OpenNextion ESP32-S3 显示屏开发板：

| ESPHome YAML | 开发板型号 | 尺寸 | 分辨率 | 显示驱动 | 状态 |
| --- | --- | --- | --- | --- | --- |
| `OpenNextion/ONX3248G035/onx3248g035.yaml` | [ONX3248G035][onx3248g035] | 3.5 英寸 | 320 x 480 | ST7796U | 已验证 |
| `OpenNextion/ONX2432G028/onx2432g028.yaml` | [ONX2432G028][onx2432g028] | 2.8 英寸 | 240 x 320 | ST7789 | 已验证 |

每个开发板目录都有独立 README，包含硬件说明、引脚映射、Wi-Fi 配网、Home Assistant 发现、Assist 设置和 ESPHome Device Builder 同步说明。

开发板提供麦克风和喇叭接口，但开发板本身不直接包含麦克风和喇叭模块。开发板参考链接中包含 OpenNextion 麦克风和喇叭模块的信息及购买链接，可作为可选配件参考；也可以使用其他兼容的麦克风和喇叭硬件。

请不要把为某一款显示屏构建的固件刷写到另一款显示屏上。

## 背景

我想把手头的 OpenNextion 开发板适配成 Home Assistant 语音助手的输入和输出设备。刚好看到 Home Assistant 官方文档介绍了 ESPHome wake-word voice assistant 项目，所以 fork 了这个项目，并为我的 OpenNextion 开发板完成适配。

OpenNextion 开发板很适合这类 DIY Home Assistant 语音助手项目，因为它在一块紧凑的开发板上集成了 ESP32-S3、SPI TFT 显示屏、电容触摸、麦克风输入、喇叭输出，并公开了硬件参考资料。这样可以更容易地构建一个同时带有视觉反馈和触控操作的语音卫星设备，而不需要从零开始连接显示屏、触摸屏、音频输入和音频输出。

这个 fork 保留原始 ESPHome 语音助手行为，并增加已支持 OpenNextion 开发板所需的板级配置。

## 3D 打印外壳

我也为已支持的 OpenNextion 显示屏尺寸设计了简单的 3D 打印外壳，并计划发布到 MakerWorld。需要的人可以免费下载和打印。

每个外壳都应匹配对应的 OpenNextion 显示屏尺寸，并为语音助手使用的麦克风和喇叭走线预留空间。

MakerWorld 项目链接：

- 3.5 英寸 ONX3248G035 外壳：link to be added
- 2.8 英寸 ONX2432G028 外壳：link to be added

## 当前适配工作

这个版本基于 ESPHome wake-word voice assistants，并增加 OpenNextion 多开发板支持。主要变化包括：

### 1. OpenNextion 开发板支持

OpenNextion ESPHome Voice Assistant 为以下开发板提供专用 ESPHome YAML 配置：

- [ONX3248G035][onx3248g035] 3.5 英寸显示屏
- [ONX2432G028][onx2432g028] 2.8 英寸显示屏

OpenNextion 配置统一放在 `OpenNextion/` 目录下，避免后续支持更多型号时在仓库顶层增加过多开发板目录。

### 2. 显示屏和开发板初始化

适配中增加了已支持 OpenNextion 开发板所需的显示屏初始化：

- ONX3248G035 的 ST7796U SPI TFT 面板支持
- ONX2432G028 的 ST7789 SPI TFT 面板支持
- 已支持面板的 BGR 颜色顺序
- PCF8574 IO 扩展器，用于 LCD 复位和喇叭功放控制
- 背光 GPIO 配置
- 共享 I2C 总线上的 CST826 电容触摸支持

### 3. 语音助手硬件

OpenNextion 配置暴露了 Home Assistant Assist 所需的硬件能力：

- GPIO19 / GPIO20 上的 PDM 麦克风输入
- GPIO16 / GPIO14 / GPIO15 上的 I2S 喇叭输出
- 由 PCF8574 控制的喇叭功放使能
- 用于 TTS 播放的 ESPHome speaker media player
- 使用已配置麦克风和喇叭的 ESPHome voice assistant 组件

### 4. 唤醒词、UI 和定时器行为

适配保留原始 ESPHome 语音助手 UI 流程，并适配到 OpenNextion 显示屏：

- 使用 `micro_wake_word` 的本地唤醒词检测
- 默认唤醒词：Okay Nabu
- Listening、thinking、replying、error、mute 和 timer-finished 显示状态
- Home Assistant Assist 定时器响铃时支持触摸屏停止
- ESPHome fallback AP 和 captive portal Wi-Fi 配网

## 当前验证状态

### 显示验证

<p align="center">
  <img src="docs/images/opennextion-esphome-voice-assistant-validation.jpg" alt="OpenNextion ESPHome 语音助手在 OpenNextion 显示屏上的验证效果" width="720">
</p>

- ONX3248G035 已在真实硬件上验证
- ONX2432G028 已在真实硬件上验证
- 两款 OpenNextion 开发板的显示输出已验证
- 两款 OpenNextion 开发板的 CST826 触摸输入已验证
- PDM 麦克风输入已通过 Home Assistant Assist 验证
- I2S 喇叭输出已通过 Home Assistant TTS 响应验证
- 本地唤醒词检测已使用配置的模型验证
- Assist 定时器可以在设备上响铃，并可通过触摸屏停止

### 固件验证矩阵

图例：✅ 已验证 / ⚠️ 依赖硬件或部分验证 / ⏳ 未使用

| 开发板 | 构建 | 启动 | 显示 | 触摸 | 麦克风 | 喇叭 | Assist 流程 | 定时器响铃 | 备注 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| ONX3248G035 | ✅ 已验证 | ✅ 已验证 | ✅ 已验证 | ✅ 已验证 | ✅ 已验证 | ✅ 已验证 | ✅ 已验证 | ✅ 已验证 | 3.5 英寸 ST7796U 显示屏 |
| ONX2432G028 | ✅ 已验证 | ✅ 已验证 | ✅ 已验证 | ✅ 已验证 | ✅ 已验证 | ✅ 已验证 | ✅ 已验证 | ✅ 已验证 | 2.8 英寸 ST7789 显示屏 |

## ESPHome Device Builder 使用方式

ESPHome Device Builder 只会为配置根目录下的 YAML 文件显示设备卡片。源码仍保留在本仓库的 `OpenNextion/` 目录下，但需要把主开发板 YAML 文件本身复制到 ESPHome 配置根目录。

示例：

```bash
cp OpenNextion/ONX2432G028/onx2432g028.yaml /path/to/esphome/config/onx2432g028.yaml
cp OpenNextion/ONX3248G035/onx3248g035.yaml /path/to/esphome/config/onx3248g035.yaml
```

如果希望 Device Builder Web UI 显示设备卡片，请不要把整个 `OpenNextion/ONX2432G028/` 或 `OpenNextion/ONX3248G035/` 目录复制到 ESPHome 配置目录。命令行 ESPHome 可以编译嵌套路径中的 YAML，但 Device Builder Web UI 只有在 YAML 文件位于配置根目录时才会显示设备卡片。

## Wi-Fi 配网和 Home Assistant 设置

OpenNextion 配置启用了 ESPHome fallback access point 和 captive portal。如果设备启动后无法连接到已知 Wi-Fi 网络，它会创建一个临时 Wi-Fi 热点。

1. 刷写与你的开发板匹配的固件。
2. 连接到该设备的 fallback ESPHome 热点。
3. 如果 captive portal 没有自动打开，请访问 `http://192.168.4.1/`。
4. 选择你的 2.4 GHz Wi-Fi 网络并输入密码。
5. 在 Home Assistant 中添加 ESPHome 设备。
6. 配置 Home Assistant Assist，并选择语音流水线。

语音助手建议使用稳定的 2.4 GHz 网络。信号弱或 Wi-Fi 漫游扫描可能导致 TTS 流式传输延迟，并让播放变得卡顿。

## 固件下载和刷写

当 Release 固件可用时，可以从 GitHub Release 页面下载固件。ESPHome factory binary 适合从地址 `0x0` 进行完整首次刷写。

Release 文件可以使用以下命名规则：

```text
opennextion-esphome-voice-assistant-<version>-<target>.factory.bin
```

| 目标 | 示例固件文件 | 刷写地址 |
| --- | --- | --- |
| [onx3248g035][release-downloads] | `opennextion-esphome-voice-assistant-v0.1.0-onx3248g035.factory.bin` | `0x0` |
| [onx2432g028][release-downloads] | `opennextion-esphome-voice-assistant-v0.1.0-onx2432g028.factory.bin` | `0x0` |

刷写 factory binary：

```bash
VERSION=v0.1.0
python -m esptool --chip esp32s3 -p /dev/cu.wchusbserial1110 -b 921600 write_flash \
  0x0 ./opennextion-esphome-voice-assistant-${VERSION}-onx2432g028.factory.bin
```

请按你的开发板替换 `VERSION`、串口和固件目标名称。

对于这个项目，首次安装建议完整刷写固件。除非 OTA 流程经过单独验证，否则暂不提供 OTA 固件下载。

## 本地构建、刷写和日志

这个项目使用 ESPHome 和 ESP-IDF framework。以下命令面向 OpenNextion 开发板。

### 验证配置

```bash
esphome config OpenNextion/ONX2432G028/onx2432g028.yaml
esphome config OpenNextion/ONX3248G035/onx3248g035.yaml
```

### 构建

```bash
esphome compile OpenNextion/ONX2432G028/onx2432g028.yaml
esphome compile OpenNextion/ONX3248G035/onx3248g035.yaml
```

### 刷写并查看日志

请按你的系统替换串口设备：

```bash
esphome run OpenNextion/ONX2432G028/onx2432g028.yaml --device /dev/ttyUSB0
esphome run OpenNextion/ONX3248G035/onx3248g035.yaml --device /dev/ttyUSB0
```

## 单个 Factory Binary

ESPHome 会生成一个可以从地址 `0x0` 写入的 `firmware.factory.bin` 文件。这适合使用 `esptool` 进行完整首次刷写。

如果直接从本仓库编译，文件会生成在 YAML 目录下：

```text
OpenNextion/ONX2432G028/.esphome/build/onx2432g028/.pioenvs/onx2432g028/firmware.factory.bin
OpenNextion/ONX3248G035/.esphome/build/onx3248g035/.pioenvs/onx3248g035/firmware.factory.bin
```

如果从 ESPHome Device Builder 配置根目录编译，文件会生成在该配置目录下：

```text
.esphome/build/onx2432g028/.pioenvs/onx2432g028/firmware.factory.bin
.esphome/build/onx3248g035/.pioenvs/onx3248g035/firmware.factory.bin
```

刷写 factory binary：

```bash
python -m esptool --chip esp32s3 -p /dev/cu.wchusbserial1110 -b 921600 write_flash \
  0x0 .esphome/build/onx2432g028/.pioenvs/onx2432g028/firmware.factory.bin
```

请按你的开发板替换串口和固件路径。

## Roadmap

后续计划：

- 在可行的情况下，继续让这个 OpenNextion fork 与上游 ESPHome wake-word voice assistants 保持同步
- 继续跟进上游 OpenNextion 开发板支持 PR
- 为已支持开发板补充更多照片和验证图片
- Release 流程准备好后，发布更方便使用的 release 固件
- 继续增加更多 OpenNextion ESP32-S3 显示屏开发板支持

## Credits

本项目基于 ESPHome wake-word voice assistants。感谢原项目和相关开源项目。

- ESPHome wake-word voice assistants: https://github.com/esphome/wake-word-voice-assistants
- Home Assistant: https://www.home-assistant.io/
- ESPHome: https://esphome.io/
- OpenNextion open source projects: https://github.com/OpenNextion
- OpenNextion board documentation: https://nextion.tech/wiki/

## License

本项目保留上游 ESPHome wake-word voice assistants 的许可条款。详情见 `LICENSE`。第三方库、ESPHome 组件和 Home Assistant 集成可能有各自的许可说明。

## Disclaimer

本项目不是 Home Assistant、ESPHome 或 OpenNextion 的官方产品。

刷写和使用第三方固件存在风险。刷写前请确认选择的固件与你的 OpenNextion 开发板型号完全匹配。本项目不对设备损坏、数据丢失、网络连接问题、Home Assistant 配置问题、语音助手行为或任何其他使用后果负责。

[onx2432g028]: https://github.com/OpenNextion/OpenNextion-SKU-ONX2432G028
[onx3248g035]: https://github.com/OpenNextion/OpenNextion-SKU-ONX3248G035
[release-downloads]: https://github.com/OpenNextion/OpenNextion-Example-wake-word-voice-assistants/releases
