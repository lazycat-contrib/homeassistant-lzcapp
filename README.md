# homeassistant-lzcapp

[Home Assistant](https://github.com/home-assistant/core)（Container 版）的懒猫微服打包：**只发喵喵商店，镜像模式**（`delivery.mode: mirror`）。

- **镜像**：`ghcr.io/home-assistant/home-assistant:2026.10.1`（HA Container：s6 + Python 运行时，Web 在 **8123**，配置在 `/config`）。镜像模式只做只读校验，交付时把 manifest 的镜像行改写成 `ghcr.1ms.run/home-assistant/home-assistant:2026.10.1`，不搬镜像到懒猫镜像源。
- **网络：`network_mode: host`** —— 按上游 Linux 安装文档的推荐（`--net=host`）：mDNS/SSDP 发现和局域网设备直连都依赖它。host 模式下容器监听宿主机网络，所以 upstream 写成 **`http://host.lzcapp:8123/`**（skill 里 Lucky 的先例同样写法）。副作用：占宿主机 8123 端口，被占用时要在 `configuration.yaml` 改端口并同步改 upstream。
- **运行用户**：`user: root`（HA 容器本身以 root 运行；平台建的 `/lzcapp/var/homeassistant` 属主也是 root）。
- **健康检查**：镜像是 Debian/uv 系 Python 运行时（Dockerfile 里有 `python3 -m compileall`），不赌 curl/wget，用 `python3` 请求 `/manifest.json`。
- **持久化**：`/lzcapp/var/homeassistant:/config`（配置、`.storage`、数据库全在这里）。
- **首启**：打开应用域名走 onboarding 创建房主账号。
- **未做**：`--privileged`（USB Zigbee/Z-Wave 棒直通需要它）；要设备直通得单独加设备映射或改用 HAOS。
- **版本跟踪**：`channel: stable` + `sort: semver` + `tag_regex: '^[0-9]+\.[0-9]+\.[0-9]+$'`（CalVer 三段式，排除 `stable`/`beta`/`dev`/`rc`）。
- **图标**：HA 品牌资源站 `brands.home-assistant.io`。
- 本包只打包 **HA Container**：没有 Supervisor，也就没有"加载项（Apps）"；要加载项需用 HAOS 形态。

## 商店状态

- **喵喵商店**：`cloud.lazycat.app.homeassistant` 由 Action 发布（`stores.private`）。
- **官方商店**：镜像模式不满足官方商店「镜像必须来自 `registry.lazycat.cloud`」的要求，只发喵喵商店。
