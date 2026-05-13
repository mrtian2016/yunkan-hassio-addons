# Changelog

## 0.7.7

- bump 镜像 tag 到 `0.7.7`：主项目源码端口段已原生 +15326 偏移到 23000-25000，
  加载项 Dockerfile **不再需要 sed-patch** 镜像内的 mediamtx.yml / config.toml /
  nginx.conf / s6-rc.d 配置，HEALTHCHECK 和 `SKYVIEW_PUBLIC_PORT` 在镜像里已
  指向 23406。`yunkan/Dockerfile` 简化到只剩 `/data` 软链一条 `RUN`。
- 删掉 `public_host` / `public_scheme` / `public_port` / `log_level` 4 个加载项
  选项：这些 env 只影响 mDNS 广播 / Origin header / loguru 级别（实际上
  `log_level` 没接到 loguru），让用户误以为能改 nginx/mediamtx 监听端口。
  端口在镜像里 hardcode，加载项不暴露假配置项。剩下唯一选项 `timezone`。

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
