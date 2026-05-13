# Changelog

## 0.7.3

- 首个 Home Assistant 加载项版本。
- 基于 `registry.cn-hangzhou.aliyuncs.com/skyview/skyview-cpu:0.7.3`。
- 仅支持 amd64。
- `host_network` 模式，复用云瞰镜像的 `+10000` 偏移端口（18080 / 18554 / 18888 / 18889 / 18189）。
- 容器内 `/app/data` 软链到 HA 持久卷 `/data`。
