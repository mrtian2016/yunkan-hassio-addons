ARG BUILD_FROM
FROM ${BUILD_FROM}

# HA 加载项把 /data 作为持久卷（宿主机 /usr/share/hassio/addons/data/<slug>），
# 而云瞰镜像内硬编码使用 /app/data。把后者软链到前者，让 DB / 录像 / 模型 /
# cookies 自动落到 HA 持久存储里，重启 / 升级加载项不丢数据。
#
# 端口已在 0.7.7 起原生 +15326 偏移到 23000-25000 段（入口 23406 / RTSP 23880
# / 内部流媒体 24214 / WebRTC 媒体 23515），不再需要在镜像里 sed 任何配置。
# HEALTHCHECK 和 SKYVIEW_PUBLIC_PORT 也已经在镜像里指向 23406。
# 公网直连端口（默认 23443）是唯一由用户在网页后台选的端口，不在这里硬编码。
RUN rm -rf /app/data \
    && ln -sfn /data /app/data
