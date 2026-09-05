# 局域网视频播放器 · LanPlay

中文 · [English](./README_EN.md)

在 Android 手机或平板上，直接播放电脑、NAS 共享文件夹里的视频，支持字幕和断点续播。

**使用条件：** Android 8+，可访问的 SMB 共享文件夹；目前提供源码，需要自行构建 APK。

[构建与安装](#自己构建) · [界面预览](#界面) · [使用与排错](./docs/OPERATIONS.md) · [电脑端海报工具](./lanplay-scraper/README.md)

## 它能做什么

- **浏览共享视频**：扫描局域网、发现共享文件夹，支持访客或账号登录、搜索与排序。
- **播放与字幕**：Media3 主内核可自动回退到 libVLC，支持硬件解码、倍速、画面适配和帧率匹配；自动匹配外挂字幕，可切换字符集、内嵌音轨和外挂音轨。
- **记录观看进度**：断点续播、历史、书签、标签、备注、回收站，以及完整备份与恢复。
- **界面主题**：多套浅色配色与 `#17191d` 深灰暗色，覆盖浏览、详情和播放页面。
- **数据留在设备上**：没有账号系统、云同步或遥测；共享凭据在本机加密保存，日志脱敏。
- **可选海报工具**：电脑端按需生成海报和影片信息，写回共享目录，手机直接读取；无需另装转码或媒体管理服务器。

## 界面

下图来自项目内置的匿名画廊：不连接任何 SMB，不读取私人媒体，也不含真实账号或文件名。

<p align="center">
  <img src="./docs/images/gallery-preview.png" alt="匿名画廊预览" width="360" />
</p>

## 自己构建

目前仅提供源码，尚未发布可直接下载安装的签名 APK。先在仓库页面选择 **Code → Download ZIP**，解压后在项目根目录打开 PowerShell。

准备 JDK 21、Android SDK 37 和 Android Studio：

```powershell
cd LanPlay
.\gradlew.bat :app:assembleDebug --no-daemon --max-workers=2
```

debug APK 在 `LanPlay/app/build/outputs/apk/debug/`。Release 签名只从工作区之外的环境变量或签名配置读取，仓库里不含任何签名文件。

跑测试：

```powershell
cd LanPlay
.\gradlew.bat :app:testDebugUnitTest --no-daemon --max-workers=2

cd ..\lanplay-scraper
.\.venv\Scripts\python.exe -m unittest -v
```

## 技术说明

**SMB 2/3 是原生实现的。** 不经过 WebDAV 桥、不依赖第三方网关、不需要在电脑上装任何伴生服务。你在 Windows 上右键共享的那个文件夹，就是它的数据源。

**播放走双内核，而且会自己切。** Media3 覆盖绝大多数情况，遇到它无法解码的格式自动回退 libVLC，应用自动处理切换。硬解、倍速、画面适配和帧率匹配在两个内核下都可用。

**刮削器是一次性工具，不是常驻服务。** 它在 PC 上按需运行，把海报和元数据写回共享目录，然后就可以关掉。手机端只通过 SMB 读结果——所以整套东西没有任何需要 24 小时开着的管理进程。真实目录只写进已被忽略的 `config.toml`。

**凭据和日志都当敏感数据处理。** SMB 账号密码在设备上加密保存，日志输出前主动脱敏，不会把共享路径和用户名原样打出来。

刮削器的安装、配置和网络安全边界见 [`lanplay-scraper/README.md`](./lanplay-scraper/README.md)。

## 目录

```text
LanPlay/             Android 应用与 Gradle 工程
lanplay-scraper/     可选的 Windows / Python 元数据刮削器
docs/images/         脱敏后的真实预览与分享封面
播放器规格.md         已实现的产品与技术规格
设计系统.md           视觉、布局与交互规范
需求文档.md           完整需求与验收边界
```

## 现实边界

- 播放体验受 SMB 服务器本身、网络质量、厂商后台策略和设备解码能力影响——这些不在 App 能控制的范围里。
- 可选刮削器会访问公开的第三方页面；使用时请遵守所在地法律和相应站点条款。
- 首个公开版本不提供签名 APK，需要自己构建。

## 更多文档

[安装、升级、备份、恢复、排错](./docs/OPERATIONS.md) · [版本变更](./CHANGELOG.md) · [参与开发](./CONTRIBUTING.md) · [安全策略](./SECURITY.md)

## 许可

源码以 [MIT License](./LICENSE) 发布。包括 libVLC 在内的第三方库保留各自许可证，本仓库不对其重新授权。
