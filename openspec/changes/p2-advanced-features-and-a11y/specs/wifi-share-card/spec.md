## ADDED Requirements

### Requirement: Wi-Fi 分享卡片合成
WiFi 页签生成二维码后，操作区 SHALL 出现「导出分享卡」入口；点击后离屏合成 1024×1280 PNG 卡片，包含：SSID、密码明文（开放网络显示「无需密码」）、按当前样式渲染的二维码、扫码连接提示语；卡片 SHALL 适配明暗主题配色。

#### Scenario: 加密网络分享卡
- **WHEN** WiFi 页签已生成二维码（WPA、SSID 为 `Home_5G`、密码已填）且用户点击「导出分享卡」
- **THEN** 合成卡片含 SSID、密码原文、二维码与提示语，走 `dbxPlugin.saveFile` 保存为 `wifi-card-Home_5G.png`

#### Scenario: 开放网络分享卡
- **WHEN** 加密方式为「无加密」
- **THEN** 卡片密码区域显示「开放网络，无需密码」，不显示空密码占位

### Requirement: 分享卡与二维码互不干扰
导出分享卡 SHALL NOT 改变当前预览、样式状态或历史记录；分享卡记录 SHALL NOT 写入历史。

#### Scenario: 导出后预览不变
- **WHEN** 用户导出分享卡后回到预览
- **THEN** 预览仍是普通 Wi-Fi 二维码，样式与导出前一致

### Requirement: 密码可见性
卡片上的密码 SHALL 以明文呈现（供他人手动输入），入口文案 SHALL 说明「卡片将包含明文密码」。

#### Scenario: 导出前知情
- **WHEN** 用户首次点击「导出分享卡」
- **THEN** 界面或弹窗明确告知卡片包含明文密码，确认后才合成导出
