## ADDED Requirements

### Requirement: SVG 矢量导出
导出格式 SHALL 增加 SVG 选项，与 PNG 尺寸选择并存；SVG 输出 SHALL 与 Canvas 渲染 1:1 对齐：三种码点形状（方块/圆角/圆点，含 0.08 pad 间隙）、定制定位眼（透明背景下白色环）、中心 Logo（含底衬圆角块）、留白边距与透明背景；SVG SHALL 带 `viewBox` 并通过现有 `dbxPlugin.saveFile` 管线保存。

#### Scenario: 导出方点 SVG
- **WHEN** 形状为圆点、ECC 为 M、无 Logo，用户选择 SVG 格式并保存
- **THEN** 产出带 `viewBox` 的 `.svg` 文件，浏览器打开显示与预览一致的圆点矩阵与定位眼

#### Scenario: 含 Logo 的 SVG
- **WHEN** 已设置中心 Logo 且用户导出 SVG
- **THEN** SVG 内嵌 Logo 图像数据（data URL）并带底衬圆角块

#### Scenario: 透明背景 SVG
- **WHEN** 透明背景开启时导出 SVG
- **THEN** SVG 背景无填充矩形，定位眼白色环保留

#### Scenario: 复制图片不受 SVG 影响
- **WHEN** 格式为 SVG 时用户点击「复制图片」
- **THEN** 仍复制 PNG 位图（剪贴板无 SVG 支持），带既有降级路径

### Requirement: 语义化导出文件名
保存文件 SHALL 按内容生成语义化文件名：URL 取域名、WiFi 取 SSID、名片取姓名、长文本取前若干字符；SHALL 附加尺寸或格式后缀，并清洗 Windows 非法文件名字符。

#### Scenario: URL 保存命名
- **WHEN** 内容为 `https://github.com/t8y2/dbx` 且保存 512px PNG
- **THEN** 文件名为 `github.com-512px.png`（域名含路径时截断至主域+首段路径）

#### Scenario: WiFi 与名片命名
- **WHEN** WiFi 页签 SSID 为 `Home_5G`，或名片姓名为 `张三`
- **THEN** 文件名分别为 `wifi-Home_5G-512px.png`、`vcard-张三-512px.png`

#### Scenario: 非法字符清洗
- **WHEN** SSID 含 `\ / : * ? " < > |` 等字符
- **THEN** 非法字符被替换为下划线，文件名仍合法且语义可辨

### Requirement: 复制图片跟随所选尺寸
「复制图片」SHALL 按尺寸选择器的当前值导出（替代固定 512px），并保留沙箱环境下的复制文本降级路径。

#### Scenario: 跟随尺寸
- **WHEN** 尺寸选择为 1024px 且用户点击「复制图片」
- **THEN** 剪贴板中的位图为 1024×1024
