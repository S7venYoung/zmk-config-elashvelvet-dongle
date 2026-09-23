# Velvet46 ZMK 配置

这是 Velvet46 分体键盘的 ZMK 固件配置，基于 ZMK 0.4，并在此基础上保留现有键位、组合键、背光和 Nice!View 屏幕配置。

## 固件功能

- 左右两侧使用 nice!nano 控制器，通过 BLE split 连接。
- 左侧构建包含 `nice_view_custom` 屏幕；右侧构建包含 Nice!View 和 ZMK Studio 支持。
- 右侧固件启用 ZMK Studio、USB UART Studio RPC，以及 BLE 管理和设置 RPC；左右侧启用 split relay/settings RPC。
- 右侧 central 额外广播 Prospector 键盘状态，供 Prospector scanner 接收；扫描器使用独立 Zephyr 4.1 构建环境，不改变左右键盘的 ZMK 0.4 构建环境。
- Prospector scanner 使用 XIAO BLE / nRF52840，启用触控操作，并选择 Codex2 配额仪表盘主题。
- GitHub Actions 同时构建左右侧固件和 `settings_reset` 固件，并将 UF2 文件作为 `firmware` artifact 上传。
- Prospector scanner 由单独的 **Build Prospector scanner** workflow 构建，UF2 artifact 名为 `prospector-scanner-codex2`。
- 按键布局和图示见 [键位图](keymap-drawer/velvet.svg)。

## 已移除的硬件功能

本配置不包含编码器、轨迹球或鼠标控制层。相关传感器、输入处理器及鼠标行为已从键盘设备树、键位图和配置中移除。

## 下载固件

推送代码后，或在仓库的 **Actions → Build ZMK firmware → Run workflow** 手动启动构建。构建完成后，在对应运行记录的 **Artifacts** 下载 `firmware`，解压后按目标固件刷写：

- `velvet_left`：左侧键盘
- `velvet_right`：右侧键盘
- `settings_reset`：清除 ZMK 保存的设置；仅在需要重置配对或存储设置时使用

每侧键盘应刷写对应的 UF2 文件。左右固件均使用 nice!nano 目标板。

Prospector scanner 是独立的 XIAO BLE 设备，刷写 `prospector_scanner_codex2_touch.uf2`。扫描器的 Codex2 页面通过 BLE 状态广播显示键盘连接、电量、活动层和输入状态；右侧键盘固件需要与扫描器固件一起更新，才能发送对应状态数据。

## 本地构建

在安装 ZMK 所需的 west、Python 和 Zephyr 工具链后，从仓库根目录执行：

```sh
west init -l config
west update --narrow
west zephyr-export
west zmk-build -d ./build -q
```

构建目标定义在 [`build.yaml`](build.yaml)，West 依赖清单位于 [`config/west-dependency.yml`](config/west-dependency.yml)。
