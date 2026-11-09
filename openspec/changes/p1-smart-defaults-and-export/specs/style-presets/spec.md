## ADDED Requirements

### Requirement: 保存当前样式为命名预设
样式盒 SHALL 提供「保存为预设」入口，将当前样式（前景、背景、透明背景、码点形状、纠错级别、留白、Logo 与 Logo 比例）保存为命名预设，默认名「预设 N」，数量上限 8 个；达到上限时 SHALL 提示先删除。

#### Scenario: 保存预设
- **WHEN** 用户调整样式后点击「保存为预设」并确认名称
- **THEN** 预设以 chip 形式出现在样式盒顶部列表中
- **AND** 已有 8 个预设时，入口提示需先删除且不新增

#### Scenario: 预设持久化
- **WHEN** 保存预设后关闭并重开插件
- **THEN** 预设列表完整恢复（通过 `host.storage` 的 `presets` key）

### Requirement: 套用预设只换样式不换内容
点击预设 chip SHALL 将样式状态整体替换为该预设并立即重新生成；当前页签的内容 SHALL 保持不变。

#### Scenario: 套用预设
- **WHEN** 文本页签有内容且用户点击某个预设 chip
- **THEN** 颜色、形状、ECC、留白、Logo 全部切换为预设值并重新生成
- **AND** 输入框内容不变

### Requirement: 删除预设
每个预设 SHALL 可单独删除；删除 SHALL 带确认弹窗（复用既有确认弹窗模式）；「恢复默认样式」按钮 SHALL NOT 影响预设列表。

#### Scenario: 删除单个预设
- **WHEN** 用户点击某预设的删除入口并确认
- **THEN** 该预设从列表与存储中移除，其余预设不受影响

#### Scenario: 重置样式不影响预设
- **WHEN** 用户点击「恢复默认样式」
- **THEN** 预设列表保持不变

### Requirement: 宿主无存储时降级
当宿主不支持 `host.storage` 时，预设入口 SHALL 隐藏并给出与历史记录一致的不可用提示；SHALL NOT 使用 localStorage 存预设（避免与历史降级路径产生双份数据语义混淆）。

#### Scenario: 无存储环境
- **WHEN** 插件运行在无 `host.storage` 能力的宿主中
- **THEN** 保存预设入口不显示，样式盒功能（手动调样式）不受影响
