# 云瞰 (YunKan) Home Assistant Add-ons

这是云瞰的 Home Assistant 加载项（add-on）仓库。

## 安装

1. 在 Home Assistant → 设置 → 加载项 → 加载项商店 → 右上角菜单「仓库」中添加本仓库地址。
2. 在加载项商店里找到「云瞰 SkyView」，点击安装。
3. 安装完成后启动，Web UI 入口在加载项页面「打开 Web 界面」按钮，首次访问会进入 Setup 向导。

## 仅支持架构

- `amd64`（x86_64）

ARM (树莓派 / aarch64) 暂不支持。

## 镜像来源

加载项基于阿里云镜像仓库：

```
registry.cn-hangzhou.aliyuncs.com/skyview/skyview-cpu:<version>
```

国内拉取无需翻墙。
