# Changelog

## 0.7.8

- 首个 OpenVINO 加载项版本，与 `yunkan` CPU 加载项并列发布。
- 镜像基线：`registry.cn-hangzhou.aliyuncs.com/skyview/skyview-openvino:0.7.8`。
- 通过 `devices: /dev/dri:/dev/dri:rwm` + `udev: true` 透传 Intel iGPU render
  node，让 OpenVINOExecutionProvider 能跑 GPU 后端。
- runtime 选择走 backend 自动降级链（tensorrt → cuda → openvino → cpu），
  openvino 镜像里 `onnxruntime-openvino` 已装、cuda / trt wheel 不在，自然
  落到 OpenVINO，无需注入 env。`detection.device_type` 沿用镜像默认 `AUTO`。
- 端口段与 CPU 加载项一致（23406 / 23880 / 24214 / 24215 / 23515），不能与
  CPU 加载项**同时**启用。
