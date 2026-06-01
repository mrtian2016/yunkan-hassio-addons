# Changelog

## 0.9.11

镜像 tag 跟随主项目升级 0.9.10 → 0.9.11。加载项选项、端口、卷挂载**均无变化**。

- **修复升级后录像清理报错**:从较旧版本升级上来的实例,可能因数据表缺一列,导致事件录像的保留/自动清理在后台反复报错。现已修复——升级后自动补齐,无需手动操作。

## 0.9.10

镜像 tag 跟随主项目升级 0.9.9 → 0.9.10。加载项选项、端口、卷挂载**均无变化**。

本次为 bugfix 维护版：

- **修复升级后部分页面打不开**:从上一版本升级后,少数用户的「设备管理」「智能联动」「实时」页面可能空白打不开,现已修复——升级后自动恢复,无需手动操作。
- **车牌号看得更清**:识别框的描边不再压住车牌本身,号码更清晰。
- **事件列表更好用**:缩略图改为更贴合画面的横向比例,车牌 / 人脸识别结果直接显示在列表里,未读标记更清爽。
- 其它修复:iOS「全部已读」后列表不刷新、网页端回放时间轴拖动更跟手(滚轮以光标位置为锚点缩放)。

## 0.9.9

镜像 tag 跟随主项目升级 0.9.2 → 0.9.9。加载项选项、端口、卷挂载**均无变化**。

本次为大版本更新，主要新增 / 改进：

- **AI 智能摘要**:检测到的事件自动生成一句自然语言描述,所有推理在本机完成,无需任何云端 API key。
- **事件智能搜索**:支持按关键词 + 语义搜索历史事件(搜「快递」也能命中「包裹」)。
- **实时画面叠加检测框**:实时预览可叠加识别框与标签,一键开关。
- **识别更准**:全新自训练模型;检测区 / 忽略区可按目标类型生效;车牌识别更稳并并入对应车辆事件。
- **事件类型名称统一**:对齐行业通用叫法(人形检测 / 车辆检测 / 移动侦测 等),手机端与网页端一致。
- 多项稳定性修复:在线 / 离线状态误报、录像片段被切短、检测框漂移、推送图文不符等。

## 0.9.2

镜像 tag 跟随主项目升级 0.9.1 → 0.9.2。加载项选项、端口、卷挂载**均无变化**。

- **首次初始化简化**:Web Admin 第一次打开不再需要选择数据库引擎,直接进入
  「创建管理员账号」页面。数据库统一用内置 SQLite 自动初始化,对用户不可见
  (HA 加载项本就默认 SQLite,此变化主要简化首次设置体验)。

## 0.9.1

镜像 tag 跟随主项目升级 0.9.0 → 0.9.1。加载项选项、端口、卷挂载**均无变化**。

- **License 激活后心跳即时启动**:修复在 Web Admin 输入激活码后,心跳要等下次
  加载项重启才上报的 bug;现在 activate / rebind 完成即起跑,License 状态、
  IAP / OTA 元数据秒级同步,不必再手动重启加载项。

## 0.9.0

跟随主项目跨 minor 版本一次升级(0.7.8 → 0.9.0)。**建议升级前先备份 HA 配置**
(Settings → Add-ons → 备份)。

- 镜像源 namespace 修正:`skyview/skyview-cpu` → `yunkan/yunkan-cpu`
  (build.yaml + README 同步)。
- 主项目 0.7.8 → 0.9.0 用户可见变化(详见主项目 CHANGELOG):
  - **磁盘自动清理**:新增 `cleanup` 独立服务,超过保留天数 或 磁盘占用超阈值
    时自动清理已上传/已删除录像,可配 `dry_run` 空跑模式。
  - **License 升级**:支持 Apple IAP 订阅(iOS)与 self-host 卡密双 source 合并。
  - **直播鉴权重构**:mediamtx HLS/WebRTC 端口绑 127.0.0.1,所有外部播放走
    nginx 反代 + JWT 短 token,客户端 80% TTL gapless 续期(看 Live 无感知)。
  - **摄像头管理重设计**:Cameras 页 + 添加流程 + 直播弹窗整套重做;add 后台
    立即抓首帧,免去等 snap 进程 30s 才出缩略图。
  - **Web Admin 服务管理**:跨进程重启 detection / automation / uploader / snap
    / postprocess / cleanup,无需重启整个加载项。
  - **VAAPI 解码容错**:对 H.265 摄像头注入 `+discardcorrupt` 丢损坏帧,
    Intel iGPU 解码 HEVC 不再因 B/P 帧引用缺失整路掉线。
- 端口、卷挂载、加载项选项**均无变化**(仍只有 `timezone` 一项)。

## 0.7.8

- bump 镜像 tag 到 `0.7.8`(主项目 0.7.7 因 HTTP 部署登录死循环 + Nuitka build
  卡 NVIDIA CDN 撤回,跳到 0.7.8)。
- 此前 0.7.7 仓库 commit 已切换镜像 tag,本次只 follow-up bump 配置 / CHANGELOG /
  README 文档,加载项 Dockerfile 本身不变(还是只剩 `/data` 软链一条 RUN)。

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
