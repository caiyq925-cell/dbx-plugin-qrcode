## ADDED Requirements

### Requirement: Wi-Fi 密码明文提示
WiFi 页签生成二维码期间，输入区 SHALL 显示固定提示：二维码包含明文密码，任何设备扫码即可联网，请勿将码图发到不可信渠道。

#### Scenario: 生成 Wi-Fi 码时可见
- **WHEN** WiFi 页签生成了有效二维码
- **THEN** 提示文案显示在 WiFi 输入区，且中英文随宿主语言切换

### Requirement: 提示不阻断且不重复弹窗
隐私提示 SHALL 为常驻轻文案（非弹窗），SHALL NOT 阻止生成、复制或保存；同名片页签不显示该提示。

#### Scenario: 不干扰主任务
- **WHEN** 用户在 WiFi 页签完成输入到保存的全流程
- **THEN** 提示始终为静态文案，不出现任何模态打断
