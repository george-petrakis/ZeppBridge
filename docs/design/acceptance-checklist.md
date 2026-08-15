# ZeppBridge 视觉与设备 UI 验收清单

> 更新：2026-08-15（最终集成 QA）。实现状态以当前工作树为准；现场账号/设备验收不能用浏览器预览数据替代。

## 全局视觉与交互

- [x] 深色基础 token 使用 `#14160C` / `#1B1E12` / `#F3F4EC` / `#CDDC7C`。
- [x] 心率、配速、消耗、海拔、步频、训练、准备度及睡眠阶段使用语义色，卡片不依赖彩色外发光。
- [x] 页面使用 CSS Grid 与响应式断点；动画仅使用 `transform` / `opacity`，并提供 `prefers-reduced-motion` 降级。
- [ ] 浅色模式切换尚未接入本轮 UI，未执行浅色截图验收。
- [x] 加载、空数据、错误、刷新中和缓存回退状态均有可见文案或占位内容。

## 真实设备识别

- [x] App、概览和设置调用 `backend.getDeviceProfiles(refresh?)`，设备名称来自 `canonical_name` / `display_name`，没有 T-Rex 3 或 Helio Ring 硬编码。
- [x] Zap Cloud 独立显示为云服务；普通 UI 不展示完整 `device_id` / serial。
- [x] 状态文案限定为“账号已识别 / 最近有数据 / 使用缓存 / 未识别”，不使用“蓝牙已连接”。
- [x] 设置页“重新识别设备”使用 `refresh=true`，失败时保留并标明本机缓存。
- [ ] 使用真实账号打开 App、概览、设置，核对设备图片、固件和最近数据时间与后端返回值。

## 健康概览

- [x] 恢复准备度来自真实 `HealthOverview` 字段，和 AI 交接就绪度分开呈现；缺失值显示“未提供/尚未获取”。
- [x] 最新睡眠、训练负荷、近期睡眠/运动摘要只渲染后端已有字段，不推算建议。
- [x] AI 交接卡只描述字段覆盖，不把数据完整性当作身体恢复结论。
- [ ] 在无数据、部分数据和 API 错误三种真实响应下分别核对视觉层级。

## 睡眠详情

- [x] `stage[]` 时间轴使用真实 ISO 时间范围；轴标签仅格式化显示时间。
- [x] 深睡、浅睡、REM、清醒固定使用睡眠语义色；时长、占比、入睡/醒来、评分缺失时不造数。
- [x] 删除依据评分推断恢复/深睡良好的文案；设备标识仅显示掩码。

## 运动详情与 GPS

- [x] 顶部表现总结仅显示真实距离、时长、配速、心率、消耗、训练负荷、VO₂max 字段；缺失字段隐藏或标记未提供。
- [x] 训练洞察只做 min/max/avg 等客观统计，不提供医疗或训练处方。
- [x] 本地 GPS 画布按时间关联最近配速样本，使用有效配速 P10/P90；暂停、异常跳点和大时间差断线。
- [x] 有效配速不足 3 个时退化为单一品牌色；图例包含起终点、暂停、方向和快慢含义，不请求地图瓦片。
- [ ] 使用真实带轨迹/暂停的运动记录做一次桌面截图核对。

## 自动化门禁

- [x] `npm run icons:generate`、`npm run icons:verify` 通过；锐角双轨 Z 图标与透明度校验通过。
- [x] `py -3 scripts/assets/verify-device-assets.py` 通过：52 entries、51 active supported、50 canonical、51 asset keys 一一对应。
- [x] `npm run build`（vue-tsc + Vite）通过；仅保留既有 chunk 体积提示。
- [x] `cargo fmt --manifest-path src-tauri/Cargo.toml --all -- --check`、`cargo check --locked --all-targets`、`cargo clippy --locked --all-targets -- -D warnings`、`cargo test --locked --jobs 1` 通过（62 tests）。
- [x] `npm run tauri build` 通过，NSIS/MSI 与便携版均生成。
- [x] 已安装全局 Playwright 1.58.0 DOM/视觉 smoke（未安装项目依赖）：1280×800、1024×768、520×800 覆盖概览/导出与提示词/设置；空数据状态可见、7 个 AI provider、隐私与 GPS 控件可见，无 console error 或横向溢出。截图保存在 `%TEMP%\zeppbridge-smoke-*.png`。
- [ ] 真实桌面账号/设备截图通过后，补录截图路径和运行时间。

## 安装版与现场数据

- [x] 使用本轮 NSIS 对现有安装执行静默升级（退出码 0）；安装 EXE 启动后持续存活至少 10 秒，开始菜单/桌面快捷方式与 EXE 图标存在。
- [x] 升级前备份并对比 app-data 元数据；未读取或修改 Credential Manager，未清理用户数据。
- [ ] 使用已保存登录态完成设备刷新，现场确认 T-Rex 3 与 Helio Strap 的规范名称；当前缓存没有可证明的刷新结果。
