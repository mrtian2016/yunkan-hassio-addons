# 云瞰 VMS (OpenVINO) — Home Assistant Add-on

OpenVINO 变体，**支持 Intel iGPU 推理加速**。如果你的 HA 主机是 J4125 / N100 /
N305 / Core i3-12100 等带 UHD 6xx / Iris Xe / Arc iGPU 的机型，请装这个加载
项；纯 CPU 推理请用并列的「云瞰 VMS」加载项。

- 镜像：`registry.cn-hangzhou.aliyuncs.com/skyview/skyview-openvino:0.7.8`
- 架构：仅 `amd64`
- 网络：`host_network`（WebRTC / RTSP / mDNS 多端口必需）
- 设备：`/dev/dri:/dev/dri:rwm`（Intel iGPU render node 透传）
- 持久化：HA 自动管理 `/data`（容器内软链到 `/app/data`）

## 为什么用 OpenVINO

| 机型 | CPU EP yolo26n | OpenVINO GPU yolo26n |
| --- | --- | --- |
| J4125 (UHD 600 / Gen9.5) | ~0.5 fps @1440p | ~3–4 fps |
| N100 (UHD Gen12) | ~2 fps | ~10–15 fps |
| Core i3-12100 (UHD 730) | ~4 fps | ~20+ fps |

实测以 main stream `2560×1440` + yolo26n + 单摄像头为参考；多摄像头共享一个
iGPU，吞吐按摄像头数线性掉。

## 端口

与 CPU 加载项**完全一致**，所以**两者不能同时启用**（端口会冲突）。

| 端口 | 协议 | 用途 |
| --- | --- | --- |
| 23406 | TCP | HTTP Dashboard / API |
| 23880 | TCP | RTSP 直连 mediamtx |
| 24214 | TCP | HLS |
| 24215 | TCP | WebRTC WHEP signaling |
| 23515 | UDP | WebRTC ICE |

## 选项

| key | 默认 | 说明 |
| --- | --- | --- |
| `timezone` | `Asia/Shanghai` | 容器时区 |

OpenVINO 设备目标 (`AUTO` / `CPU` / `GPU` / `NPU`) 不通过加载项选项暴露——镜像
默认 `detection.device_type = "AUTO"`，由 OpenVINO 自己挑可用的最快设备
（iGPU 透传成功就走 GPU，否则降级 CPU）。AUTO 已经能覆盖 99% 的场景，
要强制走某个设备请用独立 docker compose 部署并自挂 `config.toml`。

## 检查 iGPU 是否透传成功

加载项启动后看日志：

```
INFO  | detection | skyview.services.detection.runtime.onnx_session:352 - ONNX session loaded: ... runtime=openvino providers=['OpenVINOExecutionProvider', ...]
```

`providers` 里有 `OpenVINOExecutionProvider` 就说明 OpenVINO 已生效；如果只有
`CPUExecutionProvider` 说明 `/dev/dri` 没透进来或 i915 内核模块没加载，HA
主机 `lsmod | grep i915` 检查一下。

## 首次启动 / License / OTA / 故障排查

与 CPU 加载项完全相同，详见姊妹加载项「云瞰 VMS」README 对应章节，这里不重复。

## 已知限制

- 仅 amd64。Apple Silicon Mac mini / 树莓派等不支持。
- iGPU 必须是 Intel Gen9 (Skylake / Apollo Lake) 或更新；Bay Trail / Cherry
  Trail 等 Gen8 之前的不行。
- AMD APU / NVIDIA GPU 走这个加载项**不会有任何加速**，HA 加载项里也不方便
  挂 NVIDIA runtime。CUDA / TensorRT 部署请用独立 docker compose。
- 与 Frigate 等同样依赖 iGPU 的加载项共享 `/dev/dri`，并发跑会互相挤压
  Execution Unit 时间片。
