# 云瞰 SkyView — Home Assistant Add-on

把云瞰单镜像直接装进 Home Assistant，省去单独 docker compose 部署。

- 镜像：`registry.cn-hangzhou.aliyuncs.com/skyview/skyview-cpu:0.7.3`
- 架构：仅 `amd64`
- 网络：`host_network`（WebRTC / RTSP / mDNS 多端口必需）
- 持久化：HA 自动管理 `/data`（容器内软链到 `/app/data`）

## 端口

| 端口 | 协议 | 用途 |
| --- | --- | --- |
| 18080 | TCP | HTTP Dashboard / API（nginx 总入口） |
| 18554 | TCP | RTSP 直连 mediamtx |
| 18888 | TCP | HLS |
| 18889 | TCP | WebRTC WHEP signaling |
| 18189 | UDP | WebRTC ICE |

加载项启动后浏览器访问 `http://<HA-IP>:18080/`，或点加载项页面「打开 Web 界面」。

## 选项

| key | 默认 | 说明 |
| --- | --- | --- |
| `timezone` | `Asia/Shanghai` | 容器时区，影响日志时间戳与静默时段 cron |
| `log_level` | `info` | s6-overlay / loguru 日志级别 |
| `public_host` | 空 | 外部访问的主机名/IP，留空则由请求 Host 推导。套 nginx 反代时填域名 |
| `public_scheme` | `http` | 外部访问的协议，反代套 HTTPS 时改 `https` |
| `public_port` | `18080` | 外部访问的端口 |

## 首次启动

1. 安装并启动加载项。
2. 打开 Web UI，进入 Setup 向导，选 SQLite（最简）或 MySQL/MariaDB。
3. 创建首位管理员账号。
4. 在「摄像头」页添加 RTSP / ONVIF 相机。

## License

云瞰是商业软件，需要 license 激活。HA 加载项里的机器指纹基于容器内的
`/etc/machine-id`，HA Supervisor 在重启加载项时保持稳定，但**卸载并重装加载项
会重新生成指纹**，可能消耗 license slot。

## OTA / 升级

不要走云瞰内置的 OTA。请通过 HA「加载项商店 → 云瞰 → 更新」走 Supervisor 标准
升级流程：加载项仓库里 `config.yaml` 的 `version` 与 `build.yaml` 的镜像 tag
同步 bump 后，HA 自动拉新镜像并 recreate 容器。

## 已知限制

- 仅 amd64。ARM（树莓派 / aarch64）暂不支持。
- 不支持 NVIDIA CUDA / Intel iGPU 硬件加速（HA 加载项无法稳定挂 `/dev/dri` 或 GPU），
  检测全部走 CPU。需要硬件加速请用独立 docker compose 部署而不是 HA 加载项。
- 私有协议摄像头（Tapo / 小米 / Wyze）需自行部署 go2rtc 桥接为标准 RTSP。

## 故障排查

- 加载项启动后 webui 打不开：HA 加载项日志看 s6 启动序列，等 `api ready` 和
  `nginx started`，首次启动加载模型可能耗时 30-60s。
- WebRTC 拉流黑屏：确认加载项以 `host_network: true` 启动（本仓库默认就是），
  且 HA 主机防火墙没拦截 18189/udp。
- License 激活失败：检查 HA 主机能否访问 `https://license.yun-kan.com`。
