## ADDED Requirements

### Requirement: 弹窗焦点管理
确认弹窗打开时焦点 SHALL 移入弹窗（默认取消按钮）；`Escape` SHALL 关闭弹窗；关闭后焦点 SHALL 返回触发元素。焦点 SHALL NOT 移出弹窗外可交互元素之外形成失焦死区。

#### Scenario: 键盘完成一次确认操作
- **WHEN** 用户以键盘触发「清空历史」并在弹窗中按 `Tab`/`Enter`/`Escape`
- **THEN** 焦点在弹窗内两按钮间循环，`Enter` 执行当前按钮，`Escape` 关闭且焦点回到「清空历史」按钮

### Requirement: 页签键盘导航
页签栏 SHALL 支持方向键左右切换焦点与激活页签，`Home`/`End` 跳转首尾；`aria-selected` 与焦点顺序保持一致。

#### Scenario: 方向键切换页签
- **WHEN** 焦点在页签上按右方向键
- **THEN** 焦点与激活态移动到下一页签，对应输入面板切换并触发重新生成

### Requirement: 动态消息可感知
Toast SHALL 带 `role="status"`（或等价 aria-live）使读屏器可感知保存/复制结果；历史删除按钮的 `aria-label` 与导出尺寸选择器的可访问名称 SHALL 随语言本地化（替换硬编码 `delete`、`PNG size`）。

#### Scenario: 本地化可访问名称
- **WHEN** 宿主语言为 zh-CN
- **THEN** 历史删除按钮的 `aria-label` 为中文，尺寸选择器可访问名称为中文

### Requirement: 触控与点击目标
历史条目删除按钮的命中区 SHALL 不小于 32×32px；全部按钮类交互元素 SHALL 保持可见标签或 `aria-label`。

#### Scenario: 删除按钮命中区
- **WHEN** 历史列表渲染
- **THEN** 每个删除按钮的实际命中区 ≥32×32px（视觉图标可小于命中区）

### Requirement: 保存快捷键
`Ctrl+S` / `Cmd+S` SHALL 触发保存动作（等同点击「保存 PNG」，含禁用态判断），并阻止浏览器默认保存行为；焦点在输入框内时 SHALL 同样生效。

#### Scenario: 快捷键保存
- **WHEN** 二维码已生成且焦点在文本输入框内，用户按下 `Ctrl+S`
- **THEN** 触发保存管线（宿主环境弹出保存对话框），无浏览器默认对话框
