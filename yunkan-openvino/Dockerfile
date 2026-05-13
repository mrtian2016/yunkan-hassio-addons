ARG BUILD_FROM
FROM ${BUILD_FROM}

# HA 加载项把 /data 作为持久卷（宿主机 /usr/share/hassio/addons/data/<slug>），
# 而云瞰镜像内硬编码使用 /app/data。把后者软链到前者，让 DB / 录像 / 模型 /
# cookies 自动落到 HA 持久存储里，重启 / 升级加载项不丢数据。
#
# OpenVINO 变体 vs CPU 变体的唯一区别是 ONNX Runtime 装的是
# onnxruntime-openvino，本加载项额外通过 config.yaml 的 devices: /dev/dri
# 把宿主机 render node 透传进容器。runtime 选择由 backend 的
# _resolve_runtime() 自动降级链 (tensorrt → cuda → openvino → cpu) 负责：
# openvino 镜像里 onnxruntime-openvino 已装、CUDA / TensorRT wheel 没装，
# 自然落到 OpenVINOExecutionProvider，不需要在加载项里注入任何环境变量。
RUN rm -rf /app/data \
    && ln -sfn /data /app/data
