## Why

当前 `ui/index.html` 是 600px 单列布局，输入区、样式盒、预览、操作行、历史垂直堆叠：预览（产品的核心实时反馈回路）被表单挤出首屏，调整样式时看不到结果；同时 WiFi/名片页签没有容量计数，超容或漏填必填字段时错误态只有一句通用文案，用户无法自助恢复。

## What Changes

- 桌面端 ≥900px 改为双栏：左栏 = 页签 + 输入区 + 样式盒（折叠态）；右栏 = 预览（画布放大至约 340px）+ 操作区，右栏 `position: sticky`
- 「保存 PNG」主按钮独占一行；尺寸选择弱化为前置小控件；历史记录移入右栏操作区下方（不再位于整页最底）
- vCard 表单改为姓名、手机外露，邮箱/网址/公司/职位/备注 5 字段折叠进「更多信息」`<details>`
- 移除 WiFi 加密行的空占位 `<div></div>`，「隐藏网络」复选框并入该行
- 三个页签统一在输入区下方显示「N 字符 · M 字节」容量计数，超过二维码容量约 90% 时变警示色
- 预览空状态提供 3 个示例 chip（网址 / Wi-Fi / 名片）一键填入
- 超长错误附「改用 L 级纠错」一键动作；名片缺姓名时空状态直接点名「请填写姓名」（启用现有未使用的 `toast.missing` 文案）
- `<640px` 断点：双列 `grid2` 变单列、操作区吸底（sticky bottom）
- 副标题精简为一行

## Capabilities

### New Capabilities

- `app-layout`: 页面栅格与分栏结构、桌面/窄屏断点行为、预览与操作区的归属、吸底行为
- `input-validation`: 三个页签统一的容量计数展示与接近上限警示
- `preview-states`: 预览区空状态与错误状态的上下文引导（示例 chip、缺失字段点名）与一键恢复动作（降 ECC）

### Modified Capabilities

（项目尚无 specs 目录，全部为新增能力）

## Impact

- 代码：仅 `ui/index.html`（CSS 栅格 + `generate()`/`updateActionButtons()`/`applyLocale()` 等应用层 JS）；不改动内联的 qrcodegen 库、历史记录与确认弹窗逻辑
- i18n：`copy.en` / `copy.zh` 新增计数、示例 chip、错误动作等文案键，两语言同步补齐
- 无新增权限、无后端、无外部依赖；`dbxPlugin.saveFile` / `copy` 导出管线不变
- 风险：单文件 UI 改动较大，需在宿主环境与独立浏览器两种环境回归导出与历史功能
