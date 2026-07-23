# OpenNextion ESPHome Voice Assistant

[![English](https://img.shields.io/badge/lang-English-blue)](./README.md)
[![中文](https://img.shields.io/badge/lang-中文-red)](./README.zh-CN.md)

<p align="center">
  <img src="docs/images/opennextion-esphome-voice-assistant-demo.jpg" alt="OpenNextion ESPHome 语音助手在 OpenNextion 显示屏上的运行效果" width="820">
</p>

OpenNextion ESPHome Voice Assistant 是一个面向 OpenNextion ESP32-S3 显示屏开发板的 Home Assistant 语音卫星项目。它将官方
[ESPHome wake-word voice assistants](https://github.com/esphome/wake-word-voice-assistants)
示例移植到 OpenNextion 硬件上，支持触摸屏状态 UI、麦克风输入、喇叭输出、Wi-Fi 配网、本地唤醒词检测和定时器响铃处理。

默认唤醒词：**Okay Nabu**

## 支持的开发板

公开的 `v0.1.2` release 面向两款 OpenNextion ESP32-S3 显示屏开发板：

| 显示屏型号 | 尺寸 | 分辨率 | 显示驱动 | ESPHome YAML | 状态 |
| --- | --- | --- | --- | --- | --- |
| [ONX3248G035][onx3248g035] | 3.5 英寸 | 320 x 480 | ST7796U | `OpenNextion/ONX3248G035/onx3248g035.yaml` | 已验证 |
| [ONX2432G028][onx2432g028] | 2.8 英寸 | 240 x 320 | ST7789 | `OpenNextion/ONX2432G028/onx2432g028.yaml` | 已验证 |

开发板提供麦克风和喇叭接口，但开发板本身不直接包含麦克风和喇叭模块。开发板参考链接中包含 OpenNextion 麦克风和喇叭模块的信息及购买链接，可作为可选配件参考；也可以使用其他兼容的麦克风和喇叭硬件。

请不要把为某一款显示屏构建的固件刷写到另一款显示屏上。

## 快速开始

1. 从 [GitHub Releases][release-downloads] 下载与你的开发板匹配的 `.factory.bin` 文件。
2. 从地址 `0x0` 刷写 factory binary。
3. 如果设备无法连接到已知 Wi-Fi 网络，请连接它创建的 ESPHome fallback 热点。
4. 如果 captive portal 没有自动打开，请访问 `http://192.168.4.1/`。
5. 选择你的 2.4 GHz Wi-Fi 网络并输入密码。
6. 设备被 Home Assistant 发现后，添加这个 ESPHome 设备。
7. 配置 Home Assistant Assist，并选择语音流水线。
8. 说 **Okay Nabu** 开始使用语音助手。

语音助手建议使用稳定的 2.4 GHz 网络。信号弱或 Wi-Fi 漫游扫描可能导致 TTS 流式传输延迟，并让播放变得卡顿。

## 固件下载和刷写

从 GitHub Releases 页面下载固件。ESPHome factory binary 适合从地址 `0x0` 进行完整首次刷写。

| 目标 | 固件文件 | 版本 | 刷写地址 |
| --- | --- | --- | --- |
| [onx3248g035][release-downloads] | `opennextion-esphome-voice-assistant-v0.1.2-onx3248g035.factory.bin` | `v0.1.2` | `0x0` |
| [onx2432g028][release-downloads] | `opennextion-esphome-voice-assistant-v0.1.2-onx2432g028.factory.bin` | `v0.1.2` | `0x0` |

刷写 factory binary：

```bash
python -m esptool --chip esp32s3 -p /dev/cu.wchusbserial1110 -b 921600 write_flash \
  0x0 ./opennextion-esphome-voice-assistant-v0.1.2-onx2432g028.factory.bin
```

请按你的开发板替换串口和固件文件名。对于这个 release，首次安装建议完整刷写固件。除非 OTA 流程经过单独验证，否则暂不提供 OTA 固件下载。

## 恢复出厂设置和重新配网

普通刷写 factory 固件不一定会清除已经保存的 Wi-Fi 信息。

如果需要让设备重新进入 ESPHome fallback AP 配网流程，请长按 **BOOT** 键 **10 秒**。这会触发 ESPHome factory reset，并清除已保存的运行时状态，例如 Wi-Fi 配网信息。

如果设备无法正常启动，或者你希望完全干净地重新安装，可以先执行整片擦除，再重新刷写对应的 `.factory.bin` 文件。

## Home Assistant 使用方式

Wi-Fi 配网完成后，Home Assistant 应该可以在同一网络中发现这个 ESPHome 设备。添加设备后，配置包含语音转文字、对话和文字转语音服务的 Assist 语音流水线。

设备提供：

- 使用 `micro_wake_word` 的本地唤醒词检测
- 用于 Home Assistant 语音转文字的 PDM 麦克风输入
- 用于 Home Assistant 文字转语音响应的 I2S 喇叭输出
- Listening、thinking、replying、error、mute 和 timer 状态的触摸屏 UI
- Home Assistant Assist 定时器响铃时支持触摸屏停止

## 背景

我想把手头的 OpenNextion 开发板适配成 Home Assistant 语音助手的输入和输出设备。刚好看到 Home Assistant 官方文档介绍了 ESPHome wake-word voice assistant 项目，所以 fork 了这个项目，并为我的 OpenNextion 开发板完成适配。

OpenNextion 开发板很适合这类 DIY Home Assistant 语音助手项目，因为它在一块紧凑的开发板上集成了 ESP32-S3、SPI TFT 显示屏、电容触摸、麦克风输入、喇叭输出，并公开了硬件参考资料。这样可以更容易地构建一个同时带有视觉反馈和触控操作的语音卫星设备，而不需要从零开始连接显示屏、触摸屏、音频输入和音频输出。

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

### 2. 显示屏、触摸和音频硬件

适配中增加了已支持 OpenNextion 开发板所需的硬件初始化：

- ONX3248G035 的 ST7796U SPI TFT 面板支持
- ONX2432G028 的 ST7789 SPI TFT 面板支持
- 已支持面板的 BGR 颜色顺序
- 共享 I2C 总线上的 CST826 电容触摸支持
- GPIO19 / GPIO20 上的 PDM 麦克风输入
- GPIO16 / GPIO14 / GPIO15 上的 I2S 喇叭输出
- PCF8574 IO 扩展器，用于 LCD 复位和喇叭功放控制
- 背光 GPIO 配置

### 3. 语音助手 UI 和定时器行为

适配保留原始 ESPHome 语音助手 UI 流程，并适配到 OpenNextion 显示屏：

- Listening、thinking、replying、error、mute 和 timer-finished 显示状态
- 用于 TTS 播放的 ESPHome speaker media player
- Home Assistant Assist 定时器可以在设备上响铃
- 定时器响铃时支持触摸屏停止
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

## 从源码构建

主要安装路径是使用 release 固件。如果你希望自定义或本地重新构建 ESPHome 固件，请参考 [Build from Source](docs/build-from-source.md)。

该文档包含 ESPHome Device Builder 目录说明、`esphome config`、`esphome compile`、`esphome run`、`esphome logs`，以及生成的 `firmware.factory.bin` 文件位置。

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
