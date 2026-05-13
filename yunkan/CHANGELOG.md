# Changelog

## 0.7.3-1

- 端口段二次偏移避开 HA Core 内置 go2rtc 占用的 18554/18555。所有端口在原
  `+10000` 偏移基础上再 `+5326`：
  - HTTP `18080 → 23406`
  - RTSP `18554 → 23880`
  - HLS `18888 → 24214`
  - WebRTC WHEP `18889 → 24215`
  - WebRTC ICE UDP `18189 → 23515`
  - 容器内 loopback 端口（api 18000、web 13000、redis 16379、mediamtx api
    19997 等）同步偏移，避免 `host_network` 模式下与 HA 自身共享 loopback 撞车。
- 加载项 Dockerfile 用 sed 在镜像基础上 patch `/etc/skyview/mediamtx.yml` /
  `/app/config/config.toml` / `/etc/nginx/conf.d/skyview.conf` / s6-rc.d 配置，
  并覆盖 `SKYVIEW_PUBLIC_PORT` 与 `HEALTHCHECK`。

## 0.7.3

- 首个 Home Assistant 加载项版本。
- 基于 `registry.cn-hangzhou.aliyuncs.com/skyview/skyview-cpu:0.7.3`。
- 仅支持 amd64。
- 容器内 `/app/data` 软链到 HA 持久卷 `/data`。
