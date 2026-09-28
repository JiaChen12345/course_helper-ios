# 课程助手 iOS 自签分发 Checklist

> 每项通过运行代码或观察行为验证。

## 实现完整性

- [ ] 手动触发工作流后生成可下载的 `.ipa` 产物（验证：运行 workflow dispatch，观察 `ios-ipa` artifact）。
- [ ] IPA 使用包含应用版本的文件名（验证：下载 artifact，观察文件名包含 `pubspec.yaml` 的版本）。
- [ ] IPA 具有标准目录结构（验证：列出压缩包内容，看到 `Payload/Runner.app`）。
- [ ] 标签构建发布 Android 和 iOS 两类附件（验证：对测试标签运行流程，观察 Release 同时存在 APK 与 IPA）。
- [x] iOS 权限用途完整（验证：解析应用配置，看到相机、定位、照片读取和照片保存的非空中文说明）。
- [x] iOS 权限请求处理器已启用（验证：检查 CocoaPods 构建设置，看到相机、定位和通知宏为启用状态）。

## 集成

- [ ] Android 自动构建保持有效（验证：运行 Android 作业，看到原有分架构 APK artifact）。
- [x] 手动运行默认只构建 iOS（验证：打开 Run workflow，看到平台默认值为 `ios`，运行时无需 Android 签名 Secrets）。
- [x] Release 作业等待两个平台构建（验证：查看作业依赖关系，看到 Android 与 iOS 均为前置作业）。
- [x] README 能进入完整侧载说明（验证：打开 README 链接，正确到达中文安装文档）。
- [x] 云端构建不读取 Apple 签名秘密（验证：检查 iOS 作业，未引用 Apple ID、证书、描述文件或相关 Secrets）。

## 编译与测试

- [x] 工作流和 plist 语法有效（验证：分别使用 YAML 与 plist 解析器加载，无错误）。
- [ ] 项目静态分析无新增错误（验证：运行 `flutter analyze`，期望退出码为 0，或确认剩余项均为本次改动前既有问题）。
- [ ] 所有现有自动化测试通过（验证：运行 `flutter test`，期望全部通过）。
- [ ] macOS 云端无签名构建成功（验证：Actions 的 iOS build 步骤退出码为 0）。

## 文档与合规

- [x] 安装说明覆盖下载、连接、签名、安装、信任和续签（验证：逐节阅读文档，六项均有明确操作）。
- [x] 安装说明包含凭据安全与 GPLv3 提示（验证：文档明确要求仅在 Sideloadly 本机流程输入 Apple ID，并说明二进制分发需提供对应源码）。

## 端到端场景

- [ ] Windows 用户从 Actions 下载 IPA 后可通过 Sideloadly 安装到 iPhone 15，并在 iOS 18.7.8 上启动至应用首页（验证：连接真机执行完整流程并观察首页；需要用户设备完成最终真机步骤）。
