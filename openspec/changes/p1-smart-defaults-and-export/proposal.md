## Why

v0.2（P0）解决了「看得到预览」的问题后，下一层体验差距在「防呆与复用」：裸域名会被编成纯文本码（扫开不是网页）、低对比配色扫不出来（只能人肉掏手机验证）、调好的样式无法按名复用、导出文件名全是无语义的 `qrcode-512px.png`、且只支持 PNG 位图。这些都能在纯前端、零新增权限的前提下补齐。

## What Changes

- **URL 智能补全**：文本页签检测形似域名且无协议的输入，出现「补全 https://」建议条，一键替换并重新生成；可忽略且同一条内容不再重复提示
- **对比度安全检查**：实时计算前景/背景 WCAG 对比度，低于 3:1 时预览上方出现警示条 +「恢复默认配色」按钮；仅警示不阻止
- **风格预设**：样式盒内可把当前样式（颜色/形状/ECC/留白/Logo）保存为命名预设，chip 列表一键套用（只换样式不换内容），上限 8 个，可删除；存 `host.storage`
- **SVG 矢量导出**：导出格式增加 SVG 选项，三种码点形状 + 定制定位眼 + 中心 Logo + 透明背景 1:1 翻译为矢量元素，走现有 `dbxPlugin.saveFile` 管线
- **语义化导出文件名**：按内容生成文件名（URL 取域名 / WiFi 取 SSID / 名片取姓名 / 长文本截断），替换硬编码 `qrcode-{px}px.png`；清洗 Windows 非法字符
- **复制图片跟随所选尺寸**：复制图片按尺寸选择器导出（原固定 512px）

## Capabilities

### New Capabilities

- `smart-input`: 文本页签的 URL 协议识别与补全建议
- `scannability-guard`: 前景/背景对比度实时检查、警示与一键恢复
- `style-presets`: 样式预设的保存、套用、删除与持久化
- `export-formats`: SVG 导出、语义化文件名、复制图片尺寸跟随

### Modified Capabilities

（P0 change 尚未归档，无既有 specs 可修改；本 change 与 P0 相互独立、无需求冲突）

## Impact

- 代码：`ui/index.html` 应用层（输入监听、样式盒、导出按钮、`buildPayload` 前置处理、`renderQr` 同构的 SVG 序列化器）
- 存储：`host.storage` 新增 `presets` key（历史条目结构不变）
- 依赖：无新增外部库；SVG 序列化自实现（渲染逻辑与 Canvas 版同源）
- 文件体积：预计增加 2-3KB，仍满足自包含单文件约束
- 风险：SVG 输出需与 Canvas 渲染逐形状对齐（dots/rounded 的 pad、眼睛的圆角），需解码回归
