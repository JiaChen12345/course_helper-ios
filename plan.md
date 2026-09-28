# 课程助手 iOS 自签分发 Plan

## 架构概览

保留现有 Flutter 应用和 Android 构建链路，在 GitHub Actions 中新增独立的 macOS iOS 构建作业。作业使用 Flutter 的无签名发布构建生成 `Runner.app`，再封装为标准 IPA，并上传为 Actions artifact；标签构建时，现有发布作业下载所有平台产物并附加到 GitHub Release。仓库内提供 Sideloadly 中文操作手册。

## 核心数据结构

### 构建产物

- 输入：`pubspec.yaml` 中的应用版本和当前提交源码。
- 中间结果：无签名的 iPhoneOS `Runner.app`。
- 输出：`CourseHelper_<version>_unsigned.ipa`，内部为 `Payload/Runner.app`。

### 工作流接口

- 手动接口：GitHub Actions 的 workflow dispatch，默认只构建 iOS，也可选择 Android 或全部平台。
- 发布接口：匹配 `v*` 的 Git 标签。
- 下载接口：名为 `ios-ipa` 的构建产物；标签运行时也进入对应 GitHub Release。

## 模块设计

### iOS 应用配置

**职责：** 声明应用名称、系统兼容范围以及相机、定位、照片库权限用途。

**对外接口：** iOS 在首次调用受保护能力时显示系统权限弹窗。

**依赖：** Flutter iOS Runner 和现有插件。

### iOS 云端构建

**职责：** 在 GitHub 托管的 macOS 环境解析版本、安装依赖、执行无签名构建、校验并打包 IPA。

**对外接口：** Actions artifact `ios-ipa`。

**依赖：** GitHub Actions、Flutter stable、Xcode、CocoaPods。

### 多平台发布

**职责：** 在标签运行时收集 Android APK 与 iOS IPA并创建或更新 Release。

**对外接口：** GitHub Release 下载附件。

**依赖：** Android 与 iOS 构建作业成功完成。

### 侧载文档

**职责：** 指导 Windows 用户安全地下载、签名、安装、信任和续签应用。

**对外接口：** 仓库中的中文 Markdown 文档及 README 入口。

**依赖：** Sideloadly、Apple ID、USB 数据线或已配对的 Wi-Fi 连接。

## 模块交互

开发者手动触发或推送标签后，Android 与 iOS 作业分别构建产物。iOS 作业将无签名 Runner 封装为 IPA 并上传；普通手动运行到此结束，用户从 Actions 下载。标签运行额外触发发布作业，汇总两个平台的产物并放入同一 Release。用户按照侧载文档交给 Sideloadly，由 Sideloadly在本机完成个人 Apple ID 签名和设备安装。

## 文件组织

- `.github/workflows/flutter-build.yml`：Android、iOS 和 Release 自动化。
- `ios/Runner/Info.plist`：iOS 展示名称与隐私权限用途。
- `ios/Podfile`：启用现有功能实际使用的 iOS 权限处理器。
- `README.md`：增加 iOS 下载入口。
- `docs/ios-sideload.md`：完整中文侧载和续签说明。

## 技术决策

| 决策点 | 选择 | 理由 |
|---|---|---|
| iOS 构建环境 | GitHub 托管 macOS runner + Flutter 3.41.8 | Xcode 只能在 macOS 构建；固定项目声明的 Flutter/Dart 版本可避免 runner 更新后自动迁移工程 |
| 签名阶段 | 云端不签名，本机 Sideloadly 签名 | 不向 GitHub 上传 Apple 凭据，适配免费个人签名 |
| 作业结构 | Android 与 iOS 独立作业 | 故障隔离清楚，也便于分别下载和维护 |
| IPA 打包 | 标准 Payload 目录后压缩 | Sideloadly 可识别，且结构可直接验证 |
| 系统范围 | 保持项目现有 iOS 13 最低版本 | 覆盖目标 iOS 18.7.8，不无故缩小兼容范围 |
| 发布方式 | Actions artifact + 标签 Release | 手动测试与正式版本附件两种场景均可使用 |
