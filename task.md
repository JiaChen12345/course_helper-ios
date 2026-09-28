# 课程助手 iOS 自签分发 Tasks

## 文件清单

| 操作 | 文件 | 职责 |
|---|---|---|
| 修改 | `ios/Runner/Info.plist` | 补齐中文名称和照片读取权限声明 |
| 修改 | `ios/Podfile` | 启用相机、定位和通知权限处理器 |
| 修改 | `.github/workflows/flutter-build.yml` | 增加 iOS 构建、IPA 打包、产物上传及 Release 附件 |
| 新建 | `docs/ios-sideload.md` | Windows + Sideloadly 安装和续签手册 |
| 修改 | `README.md` | 增加 iOS 自签下载入口与 GPL 提示 |

## T1: 补齐 iOS 应用配置

**文件：** `ios/Runner/Info.plist`、`ios/Podfile`

**依赖：** 无

**步骤：**

1. 将手机桌面展示名称调整为中文“课程助手”。
2. 保留现有相机、定位和照片保存用途说明。
3. 增加照片读取用途说明，覆盖选择相册图片的现有功能。
4. 在 CocoaPods 构建配置中启用代码实际请求的相机、定位和通知权限处理器。
5. 使用 plist 解析器检查 XML 和键值有效性。

**验证：** 解析 plist，期望名称及四类隐私用途键存在且中文说明非空；检查 Podfile，期望三项权限宏已启用。

## T2: 新增独立 iOS 构建作业

**文件：** `.github/workflows/flutter-build.yml`

**依赖：** T1

**步骤：**

1. 保留已有 Android 作业行为，将作业名称明确为 Android 构建。
2. 为手动运行增加平台选择，默认只运行 iOS 作业。
3. 新增 macOS 作业并安装 Flutter 依赖。
4. 从项目版本生成稳定的 IPA 文件名。
5. 运行无签名 iOS release 构建。
6. 建立 `Payload/Runner.app`，打包并检查 IPA 内容。
7. 上传名为 `ios-ipa` 的 artifact。

**验证：** 解析工作流 YAML，并检查 iOS 作业包含 macOS runner、无签名构建、结构校验和 artifact 上传步骤。

## T3: 接入多平台 Release

**文件：** `.github/workflows/flutter-build.yml`

**依赖：** T2

**步骤：**

1. 让 Release 作业同时依赖 Android 与 iOS 构建。
2. 在发布附件匹配中加入带版本号的 IPA。
3. 保持仅标签运行时创建 Release。

**验证：** 检查 Release 作业依赖和附件通配符，期望同时覆盖 APK 与 IPA。

## T4: 编写侧载文档

**文件：** `docs/ios-sideload.md`

**依赖：** T2

**步骤：**

1. 说明如何手动运行 GitHub Actions 并下载 `ios-ipa`。
2. 说明 Sideloadly、Apple 设备驱动和 iPhone 连接准备。
3. 说明 Apple ID 签名安装、开发者模式/信任处理和常见错误。
4. 说明免费签名约七天到期及重新安装方式。
5. 加入凭据安全、GPLv3 和数据风险提示。

**验证：** 按文档章节逐项检查，期望覆盖 AC4 和 N4。

## T5: 更新入口并完成静态验证

**文件：** `README.md`、上述全部修改文件

**依赖：** T1、T2、T3、T4

**步骤：**

1. 在 README 的平台说明附近加入 iOS 自签构建与安装链接。
2. 获取 Flutter 依赖并运行格式、静态分析和测试。
3. 检查 Git diff，确保没有证书、Apple ID 或生成的私密配置。

**验证：** README 链接可解析；`flutter analyze` 与 `flutter test` 通过，或记录明确的既有失败；敏感信息扫描无结果。

## 执行顺序

T1 → T2 → T3；T2 → T4；T1 + T3 + T4 → T5

T3 与 T4 可在 T2 完成后并行。
