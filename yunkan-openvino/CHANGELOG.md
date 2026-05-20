# Changelog

## 0.9.2

镜像 tag 跟随主项目升级 0.9.1 → 0.9.2。加载项选项、端口、`/dev/dri` 透传**均无变化**。

- **首次初始化简化**:Web Admin 第一次打开不再需要选择数据库引擎,直接进入
  「创建管理员账号」页面。数据库统一用内置 SQLite 自动初始化,对用户不可见
  (HA 加载项本就默认 SQLite,此变化主要简化首次设置体验)。

## 0.9.1

镜像 tag 跟随主项目升级 0.9.0 → 0.9.1。加载项选项、端口、`/dev/dri` 透传**均无变化**。

- **License 激活后心跳即时启动**:修复在 Web Admin 输入激活码后,心跳要等下次
  加载项重启才上报的 bug;现在 activate / rebind 完成即起跑,License 状态、
  IAP / OTA 元数据秒级同步,不必再手动重启加载项。

## 0.9.0

跟随主项目跨 minor 版本一次升级(0.7.8 → 0.9.0)。**建议升级前先备份 HA 配置**
(Settings → Add-ons → 备份)。

- **重要 bug 修复**:镜像源 namespace 修正 `skyview/skyview-openvino` →
  `yunkan/yunkan-openvino`。build.yaml 之前一直指向不存在的 namespace,
  HA Supervisor build 时会找不到镜像;现在能正常拉取。
- 主项目 0.7.8 → 0.9.0 用户可见变化(详见主项目 CHANGELOG):
  - **VAAPI HEVC 解码容错**(对 OpenVINO 用户尤其关键):对 H.265 摄像头注入
    `+discardcorrupt -err_detect ignore_err`,Intel iGPU 解码 HEVC 不再因
    B/P 帧引用缺失整路掉线。家用 IPC 大量走 H.265 的场景受益显著。
  - **磁盘自动清理**:新增 `cleanup` 独立服务,超过保留天数 或 磁盘占用超阈值
    时自动清理已上传/已删除录像,可配 `dry_run` 空跑模式。
  - **License 升级**:支持 Apple IAP 订阅(iOS)与 self-host 卡密双 source 合并。
  - **直播鉴权重构**:mediamtx HLS/WebRTC 端口绑 127.0.0.1,外部播放走 nginx
    反代 + JWT 短 token,客户端 80% TTL gapless 续期。
  - **摄像头管理重设计**:Cameras 页 + 添加流程 + 直播弹窗整套重做;add 后台
    立即抓首帧,免去等 snap 进程 30s 才出缩略图。
  - **Web Admin 服务管理**:跨进程重启 detection / automation / uploader / snap
    / postprocess / cleanup,无需重启整个加载项。
- 端口、`/dev/dri` 透传、`udev: true`、加载项选项均**无变化**。

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
