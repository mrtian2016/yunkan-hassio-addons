# 云瞰 (YunKan) Home Assistant Add-ons

这是云瞰的 Home Assistant 加载项（add-on）仓库。

## 安装

1. 在 Home Assistant → 设置 → 加载项 → 加载项商店 → 右上角菜单「仓库」中添加本仓库地址。
2. 仓库里现在有两个加载项，按 HA 主机硬件**二选一**安装：
   - **云瞰 VMS** — 纯 CPU 推理。任何 amd64 主机都能跑，无任何硬件依赖。
   - **云瞰 VMS (OpenVINO)** — Intel iGPU 加速（UHD 6xx / Iris Xe / Arc）。
     主机有 J4125 / N100 / N305 / Core i3-12100 等带 iGPU 的 CPU 时**强烈推荐**，
     YOLO 推理吞吐通常是 CPU 变体的 3-5x。两个加载项端口一致**不能同时启用**。
3. 安装完成后启动，Web UI 入口在加载项页面「打开 Web 界面」按钮，首次访问会进入 Setup 向导。

## 仅支持架构

- `amd64`（x86_64）

ARM (树莓派 / aarch64) 暂不支持。

## 镜像来源

加载项基于阿里云镜像仓库：

```
registry.cn-hangzhou.aliyuncs.com/yunkan/yunkan-cpu:<version>        # 云瞰 VMS
registry.cn-hangzhou.aliyuncs.com/yunkan/yunkan-openvino:<version>   # 云瞰 VMS (OpenVINO)
```

国内拉取无需翻墙。
