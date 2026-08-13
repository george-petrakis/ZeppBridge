# ZeppBridge 验收记录

版本：0.2.1  
平台：Windows x64

## 当前结论

本轮已实现同步状态语义、连接恢复、设置体验、睡眠与运动详情，并通过源码门禁、正式打包和本机安装入口烟测。公开仓库不保存真实账号标识、设备标识、局域网地址或个人健康样本；此文档只记录可复现的工程验收范围。

## 自动化门禁

| 门禁 | 预期 |
| --- | --- |
| `npm run build` | 前端类型检查与生产构建通过 |
| `cargo fmt --manifest-path src-tauri/Cargo.toml --all -- --check` | 格式检查通过 |
| `cargo check --manifest-path src-tauri/Cargo.toml --locked --all-targets` | Rust 全目标检查通过 |
| `cargo clippy --manifest-path src-tauri/Cargo.toml --locked --all-targets -- -D warnings` | 零警告 |
| `cargo test --manifest-path src-tauri/Cargo.toml --locked --jobs 1` | 全部测试通过 |
| `cmd.exe /d /c build.bat` | 生成 NSIS 与 MSI 安装包 |

## 安装版验收范围

- 已保存认证在应用重启后恢复为“已连接”，日常同步不重复要求验证。
- 云端拉取时间、同步结果和各数据流最新样本时间分别显示。
- 同步请求成功但样本未推进时显示“云端暂无新数据”。
- 同步完成后，当前概览、睡眠或运动页面自动读取最新本地数据。
- 睡眠和运动列表可进入详情；缺少真实采样或路线时不生成模拟图表。
- 跟随系统、浅色、深色主题可切换并持久化。
- 正式安装后的 EXE 与桌面快捷方式使用项目图标。

## 尚未覆盖

1. 不同地区、账号与设备组合仍需各自验证。
2. Android 用户 CA、厂商限制与证书固定可能阻止首次捕获。
3. 安装包尚未进行 Authenticode 签名。
4. 健康数据库目前未整库加密。
5. 自动更新、SBOM、干净虚拟机安装和稳定的公开 REST / MCP 接口尚未完成。

提交 issue 时请只提供最小、脱敏的复现信息，不要上传 token、用户 ID、设备 ID、数据库或完整原始响应。

