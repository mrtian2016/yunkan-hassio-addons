ARG BUILD_FROM
FROM ${BUILD_FROM}

# HA 加载项把 /data 作为持久卷（宿主机 /usr/share/hassio/addons/data/<slug>），
# 而云瞰镜像内硬编码使用 /app/data。把后者软链到前者，让 DB / 录像 / 模型 /
# cookies 自动落到 HA 持久存储里，重启 / 升级加载项不丢数据。
RUN rm -rf /app/data \
    && ln -sfn /data /app/data

# 镜像内置 s6-overlay 当 PID 1，HA 加载项 init: false 时直接复用其 ENTRYPOINT/CMD，
# 这里不覆盖。
