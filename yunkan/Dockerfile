ARG BUILD_FROM
FROM ${BUILD_FROM}

# HA 加载项把 /data 作为持久卷（宿主机 /usr/share/hassio/addons/data/<slug>），
# 而云瞰镜像内硬编码使用 /app/data。把后者软链到前者，让 DB / 录像 / 模型 /
# cookies 自动落到 HA 持久存储里，重启 / 升级加载项不丢数据。
RUN rm -rf /app/data \
    && ln -sfn /data /app/data

# 端口段二次偏移：HA Core 内置 go2rtc 占用 127.0.0.1:18554 + *:18555，加载项
# host_network 下整段 18xxx 都有撞车风险。把所有端口在原 +10000 偏移基础上
# 再 +5326，落到 23000-25000 这段冷门区间，避开 HA Core / Frigate / Z2M 等
# 常见 addon。映射表：
#   11935→17261  11936→17262  13000→18326  16379→21705
#   18000→23326  18080→23406  18100-18107→23426-23433
#   18189→23515  18322→23648  18554→23880  18888→24214
#   18889→24215  18890→24216  19996→25322  19997→25323
#   19998→25324  19999→25325
RUN set -eu \
 && for f in \
        /etc/skyview/mediamtx.yml \
        /app/config/config.toml \
        /etc/nginx/conf.d/skyview.conf \
        $(find /etc/s6-overlay/s6-rc.d -type f 2>/dev/null) \
        $(find /etc/s6-overlay/scripts -type f 2>/dev/null) \
    ; do \
        [ -f "$f" ] || continue; \
        sed -i \
            -e 's/\b19999\b/25325/g' \
            -e 's/\b19998\b/25324/g' \
            -e 's/\b19997\b/25323/g' \
            -e 's/\b19996\b/25322/g' \
            -e 's/\b18890\b/24216/g' \
            -e 's/\b18889\b/24215/g' \
            -e 's/\b18888\b/24214/g' \
            -e 's/\b18554\b/23880/g' \
            -e 's/\b18322\b/23648/g' \
            -e 's/\b18189\b/23515/g' \
            -e 's/\b18107\b/23433/g' \
            -e 's/\b18106\b/23432/g' \
            -e 's/\b18105\b/23431/g' \
            -e 's/\b18104\b/23430/g' \
            -e 's/\b18103\b/23429/g' \
            -e 's/\b18102\b/23428/g' \
            -e 's/\b18101\b/23427/g' \
            -e 's/\b18100\b/23426/g' \
            -e 's/\b18080\b/23406/g' \
            -e 's/\b18000\b/23326/g' \
            -e 's/\b16379\b/21705/g' \
            -e 's/\b13000\b/18326/g' \
            -e 's/\b11936\b/17262/g' \
            -e 's/\b11935\b/17261/g' \
            "$f"; \
    done

# 覆盖镜像 ENV：mDNS / API 自报的对外端口要跟着 nginx 走
ENV SKYVIEW_PUBLIC_PORT=23406

# 覆盖镜像 HEALTHCHECK：原 18080 已 sed 成 23406
HEALTHCHECK --interval=30s --timeout=10s --start-period=90s --retries=3 \
    CMD curl -sf http://127.0.0.1:23406/healthz || exit 1
