# ZeppBridge 前端视觉与设备 UI 改造计划

> 更新：2026-08-15。本文记录当前实现事实和现场验收缺口，不把概念图或浏览器预览当作真实设备数据。

## 目标

建立一个数据优先的健康工作台：层级清晰、状态完整、语义颜色稳定，并让设备识别、健康恢复和 AI 交接三个概念彼此独立。概览、睡眠、运动和设置页面共享同一套 token 与设备模型。

## 已落地范围

### 视觉系统

- 深色画布 `#14160C`、表面 `#1B1E12`、正文 `#F3F4EC`、品牌 `#CDDC7C`。
- 语义色：心率 `#FF6B6B`、配速 `#5B8FF9`、消耗 `#FF9F43`、海拔 `#E4C95A`、步频 `#54CFA2`、训练 `#9B7BFF`、准备度 `#75D584`。
- 睡眠阶段固定为深睡 `#6657D9`、浅睡 `#8E9BFF`、REM `#49CDA2`、清醒 `#FF6B6B`。
- CSS Grid、响应式布局、轻量 transform/opacity 动效和 reduced-motion 支持已覆盖本次改造页面。

### 设备与状态

- `useDevices` 统一消费 `getDeviceProfiles(refresh?)`，通过 device catalog 将 `image_key` 映射到本地资源。
- App/Overview/Settings 渲染账号实际设备；Zap Cloud 单独作为云服务行。
- 所有普通设备状态限定为“账号已识别 / 最近有数据 / 使用缓存 / 未识别”。设置页可刷新识别，失败会回退缓存并说明原因。
- 普通页面不显示完整设备 ID 或序列号，设置详情仅显示掩码。

### 健康与记录

- Overview 使用 `HealthOverview`、训练负荷序列、最近睡眠和运动记录；身体准备度与 AI 交接就绪度分别计算和呈现。
- SleepDetail 使用真实 stage ISO 时间轴和固定阶段色；缺字段保持“未提供/尚未获取”，不从评分推导恢复建议。
- WorkoutDetail 顶部只展示后端真实指标；统计仅为 min/max/avg 等可验证结果。
- GPS 轨迹在本地画布绘制：按时间关联最近配速样本，P10/P90 约束色阶，暂停/异常跳点/大时间差断线，配速不足时回退品牌色；不请求地图瓦片。

## 组件边界

- `src/composables/useDevices.ts`：设备查询、缓存状态、显示模型和 ID 掩码。
- `src/components/DeviceVisual.vue` / `DeviceCard.vue`：本地设备图像和统一设备信息层级。
- `src/components/StageBar.vue`：睡眠阶段条及真实 ISO 范围的格式化轴标签。
- 页面组件只负责数据编排和布局，不在 UI 层制造健康数值或设备名称。

## 验收顺序

1. 运行 `npm run build`，确认 vue-tsc 与 Vite 门禁通过。
2. 使用真实账号启动桌面应用，验证设备识别、缓存刷新、Overview 数据覆盖和设置页掩码。
3. 使用至少一条带 stage[] 的睡眠记录和一条带轨迹/暂停的运动记录，核对时间轴、断线、色阶和缺失字段。
4. 在窄窗口和浅色主题核对 Grid 重排、对比度、键盘焦点与 reduced-motion。
5. 用全局 Playwright 做轻量 DOM smoke，记录截图路径；浏览器预览只用于结构检查，不得宣称真实数据成功。

## 未完成现场门禁

- 尚未在本轮拿到真实账号/设备响应，因此设备图片、固件、最近数据时间和错误回退仍需现场截图确认。
- 尚未记录真实轨迹截图及浅色模式截图；完成后将结果补回 `docs/design/acceptance-checklist.md`。
