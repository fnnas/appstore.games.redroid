# RedroidContainer for fnOS

![RedroidContainer](package_aarch64/ICON_256.PNG)

RedroidContainer 是面向 fnOS ARM64 设备的第三方 Android 容器应用包，
基于 [redroid-rk3588](https://github.com/CNflysky/redroid-rk3588) 运行
LineageOS 20.1 环境，并通过 ADB 进行连接和管理。

本仓库提供 fnOS 应用包配置，容器镜像会在应用首次启动时拉取。

## 主要特性

- 针对 RK3588 ARM64 平台配置，并挂载 `/dev/mali0` 供容器使用
- 默认分辨率为 1080 x 1920
- 默认启用 Magisk 和模拟 Wi-Fi
- 将宿主机的 TCP 5555 端口映射至容器的 ADB 服务
- 使用 Docker 命名卷持久化 Android `/data` 数据
- 提供简体中文、英文和日语应用描述及安装提示
- 由 fnOS 应用中心管理容器的启动、停止、升级和卸载流程

## 运行要求

- RK3588 ARM64 设备
- fnOS `1.1.3100` 或更高版本
- 系统存在可用的 `/dev/mali0` 设备
- TCP 5555 端口未被其他服务占用
- 能够访问容器镜像仓库的网络环境

首次启动需要下载容器镜像，实际耗时取决于网络状况。

## 安装

1. 从项目的 [Releases](https://github.com/fnnas/appstore.games.redroid/releases)
   页面下载最新的 aarch64 `.fpk` 安装包。
2. 在 fnOS 应用中心导入安装包并完成安装。
3. 阅读安装向导中的安全和风险说明，确认后继续。
4. 等待容器镜像拉取完成并检查应用运行状态。

请通过 fnOS 应用中心管理本应用。不要手动修改应用内的
`docker-compose.yaml`，也不要绕过应用中心创建、删除、重命名或重建容器，
否则可能造成数据损坏、容器无法启动或升级与卸载异常。

## 连接 Android

应用不提供 Web 管理界面。请在可信客户端安装 Android Platform Tools，
并通过 ADB 连接 NAS：

```shell
adb connect <NAS_IP>:5555
adb devices
```

连接成功后，也可以使用支持 ADB 的工具操作 Android 环境。例如使用
scrcpy：

```shell
scrcpy --serial <NAS_IP>:5555
```

请将 `<NAS_IP>` 替换为 fnOS 设备的实际 IP 地址。

## 数据持久化

Android `/data` 使用名为 `appstore-games-redroid12-data` 的 Docker 命名卷。
应用自身的卸载脚本不会主动删除该卷，以降低卸载或应用目录变化导致数据
丢失的风险。

重要数据仍应单独备份。删除或重建该命名卷会永久清除 Android 环境中的
应用、账号和其他用户数据。

## 安全与使用风险

- ADB 5555 端口默认开放且未提供额外的访问控制，请配置防火墙，只允许
  可信设备访问。
- 容器以 `privileged` 模式运行。容器内的恶意软件可能通过设备挂载、开放
  端口或容器权限影响宿主机，请只安装可信来源的应用。
- 未经额外适配的 redroid 可能被游戏、社交、支付、金融或其他强风控应用
  识别为异常环境，导致登录受限、设备拉黑、账号封禁、资产冻结或服务
  永久不可用。
- 不要使用重要账号直接测试。使用者需自行评估并承担相关风险。

## 项目结构

```text
package_aarch64/
|-- app/docker/       # 容器编排配置
|-- app/ui/           # fnOS 应用界面配置和图标
|-- cmd/              # 应用生命周期脚本
|-- config/           # 权限及 Docker 项目声明
|-- i18n/             # 多语言文案
|-- wizard/           # 安装确认向导
`-- manifest          # fnOS 应用清单
```

GitHub Actions 会检查生命周期脚本、JSON 配置及必要文件，注入应用版本，
然后生成 aarch64 `.fpk` 安装包。版本和构建编号由
[发布工作流](.github/workflows/test_release.yml) 统一维护。

## 支持与反馈

本项目由社区用户打包和维护，并非 fnOS 官方应用。使用问题请通过本仓库的
[Issues](https://github.com/fnnas/appstore.games.redroid/issues) 反馈，不要向
系统出品方提交本应用相关问题。

