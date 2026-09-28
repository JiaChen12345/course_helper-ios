# 在 Windows 上构建并自签安装 iPhone 版课程助手

本流程适用于 iPhone 15、iOS 18.7.8，也适用于项目最低版本范围内的其他 iPhone。GitHub Actions 只生成未签名 IPA；Apple ID 只在你自己的 Windows 电脑上交给 Sideloadly 完成签名，不会上传到仓库或 GitHub Actions。

## 一、从 GitHub Actions 下载 IPA

1. 将本项目推送到你自己的 GitHub 仓库，并在仓库中打开 **Actions**。
2. 在左侧选择 **Flutter Build and Release**。
3. 点击 **Run workflow**，选择需要构建的分支，将 **Platform to build** 保持为默认的 `ios`，再点击绿色的 **Run workflow**。
4. 等待 **Build unsigned iOS IPA** 作业变成绿色。
5. 打开该次运行记录，在页面下方的 **Artifacts** 区域下载 `ios-ipa`。
6. 解压下载的 artifact ZIP，得到类似 `CourseHelper_1.1.9_unsigned.ipa` 的文件。不要再解压 IPA。

如果维护者推送了 `v*` 版本标签，也可以直接从对应 GitHub Release 下载同名 IPA。

## 二、准备 Windows 与 iPhone

1. 从 [Sideloadly 官网](https://sideloadly.io/) 下载 Windows 版本。
2. 按 Sideloadly 官方要求安装 Apple 官网提供的网页版 iTunes 和 iCloud；如果电脑中是 Microsoft Store 版本，请先卸载 Store 版本。
3. 使用数据线连接 iPhone，解锁手机，并在“要信任此电脑吗？”提示中选择“信任”。
4. 首次侧载时保持电脑和 iPhone 联网，以便 Apple 验证账号和开发者签名。

建议使用专门用于侧载的 Apple ID，并为账号开启双重认证。只在 Sideloadly 的本机窗口中输入账号信息；不要把 Apple ID、密码、验证码、证书或描述文件提交到 GitHub。

## 三、使用 Sideloadly 签名和安装

1. 打开 Sideloadly，确认顶部设备列表中已经出现你的 iPhone。
2. 将 `.ipa` 文件拖入 Sideloadly，或点击 IPA 图标选择文件。
3. 在 **Apple account** 中填写你的 Apple ID。
4. 保持默认的 **Apple ID Sideload** 安装模式；如手机中已有相同 Bundle ID 的其他版本，可在高级选项中修改 Bundle ID。
5. 点击 **Start**，按提示输入密码和双重认证验证码。
6. 等待日志出现安装成功信息，并确认“课程助手”出现在 iPhone 主屏幕。

免费 Apple ID 通常最多只能同时侧载少量应用，并且签名约 7 天有效。这是 Apple 对免费开发签名的限制，不是本项目的到期机制。

## 四、在 iOS 18 上允许应用运行

1. 如果系统要求开发者模式，打开 **设置 → 隐私与安全性 → 开发者模式**。
2. 开启开关并按提示重新启动；重启后解锁手机，再次确认开启开发者模式。
3. 如果首次启动提示开发者不受信任，打开 **设置 → 通用 → VPN 与设备管理**。
4. 选择与你 Apple ID 对应的开发者项目，按系统提示信任或验证。在 iOS 18 上，系统可能要求“允许并重新启动”。
5. 回到主屏幕启动“课程助手”，并按实际使用需要授权相机、定位、照片和通知权限。

开发者模式会降低部分系统保护，仅应在理解侧载风险时开启。删除所有自签应用且不再侧载后，可以关闭开发者模式。

## 五、七天续签

- Sideloadly 的自动刷新服务可在电脑运行、iPhone 与电脑可通信时尝试定期重新签名。
- 也可以在签名到期前重新连接 iPhone，用同一个 Apple ID 再次安装同一个 IPA；通常不会清除应用数据，但重要数据仍应提前备份。
- 如果应用已经无法打开，重新执行“使用 Sideloadly 签名和安装”即可。
- 更新到新的 IPA 时，同样使用相同 Apple ID 和 Bundle ID 覆盖安装。

## 六、常见问题

### Sideloadly 看不到 iPhone

- 解锁 iPhone，并重新插拔数据线。
- 确认已经在手机上选择“信任此电脑”。
- 确认安装的是 Sideloadly 要求的 Apple 官网网页版 iTunes/iCloud，而不是 Microsoft Store 版。
- 重启 Apple Mobile Device Service、Sideloadly 和 iPhone 后再试。

### 提示无法验证或应用不受信任

- 确认 iPhone 已联网。
- 检查 **设置 → 通用 → VPN 与设备管理** 中是否可以验证你的开发者项目。
- 检查 **设置 → 隐私与安全性 → 开发者模式** 是否开启。
- 公司或学校管理的设备可能通过 MDM 禁止侧载或信任新开发者，此时需要联系设备管理员。

### 签名或安装失败

- 在 Sideloadly 日志中查看具体错误，确认 Apple ID 和验证码有效。
- 免费账号达到侧载应用或 App ID 数量限制时，删除不再使用的自签应用后重试。
- 发现 Bundle ID 冲突时，在 Sideloadly 高级选项中更换为个人唯一值。
- 重新下载 Actions artifact，排除 ZIP 或 IPA 下载不完整。

## 七、安全、许可与功能说明

- 本应用会登录第三方课程平台。请自行判断账号和数据风险，不要使用来源不明的 IPA。
- Actions 生成的是未签名构建，仓库不会接触你的 Apple 凭据；最终签名只发生在你的电脑上。
- 本项目采用 GPLv3。向他人分发原版或修改版 IPA 时，必须同时提供完整对应源码、许可证和原始版权声明。
- 第三方课程平台接口、签到规则或风控变化可能导致部分业务功能失效；构建成功不等于这些外部接口永久可用。

## 官方参考

- [Sideloadly 官方网站与常见问题](https://sideloadly.io/)
- [Apple：在设备上启用开发者模式](https://developer.apple.com/documentation/xcode/enabling-developer-mode-on-a-device)
- [Apple：在 iOS 18 上为手动安装的 App 建立信任](https://support.apple.com/zh-cn/118254)
