## 1. 布局栅格与 DOM 调整（app-layout）

- [x] 1.1 CSS：新增 `@media (min-width: 900px)` 双栏 Grid（左栏 `1fr`、右栏 `380px` sticky），`main` 最大宽度放宽至 `min(980px, 100%)`
- [x] 1.2 DOM：将预览区、操作行（尺寸/保存/复制）、历史 `<details>` 包进右栏容器 `<aside>`；左栏保留页签 + 面板 + 样式盒
- [x] 1.3 操作区改造：「保存 PNG」独占一行；尺寸选择缩为小控件，与两个复制按钮同排
- [x] 1.4 vCard 表单分层：姓名、手机外露，邮箱/网址/公司/职位/备注移入 `<details>「更多信息」`（复用样式盒折叠模式）
- [x] 1.5 WiFi 表单紧凑化：删除加密行空占位 `<div></div>`，隐藏网络复选框移入该行
- [x] 1.6 副标题文案精简为一行（`copy.en` / `copy.zh` 同步改）
- [x] 1.7 窄屏断点：`<640px` 时 `grid2` 单列、操作区 `sticky bottom` 吸底；`<900px` 退回单列并保持「输入 → 预览 → 操作」顺序

## 2. 容量计数与警示（input-validation）

- [x] 2.1 实现 `capacity(ecc)` 查表计算（基于 qrcodegen 内部 codeword 表推导 byte 模式上限）
- [x] 2.2 `generate()` 中三个页签统一更新计数（WiFi/vCard 按最终转义载荷计算），替换现有仅文本页签的 `textMeta` 更新逻辑
- [x] 2.3 计数 ≥90% 容量时加警示 class（warn 色），ECC 切换时重算阈值；空载荷显示 0 且不警示
- [x] 2.4 i18n：`copy.en/zh` 计数与警示文案键，`applyLocale()` 注入

## 3. 空态/错误态上下文化（preview-states）

- [x] 3.1 空状态新增 3 个示例 chip（网址/WiFi/名片），点击 → 切页签 + 填示例 + `scheduleGenerate(0)`，不写入历史
- [x] 3.2 缺字段点名：载荷为空但页签有部分输入时，`#empty` 显示点名文案（启用并接线现有 `toast.missing(f)`）
- [x] 3.3 超容错误附实际字节数；ECC 非 L 时显示「改用 L 级纠错重试」按钮 → 置 `style.ecc="L"` + `syncStyleInputs()` + 重新生成；ECC 已 L 仅显示缩短提示
- [x] 3.4 i18n：示例 chip、点名、降级按钮文案（中英同步）

## 4. 回归与验收

- [x] 4.1 `dbx-plugin dev --port 5190` 实测宿主面板宽度，确认 900px 断点是否需要下调；调整后提交
- [ ] 4.2 宿主 + 独立浏览器双环境手工回归：保存 PNG（3 尺寸）、复制图片/内容降级、历史记录/恢复/清空、样式重置、明暗主题、中英文（独立浏览器侧已自动化回归通过；宿主侧保存/复制/历史走宿主 API，待用户在 DBX 宿主中人工确认）
- [x] 4.3 对照 specs 逐条验证：双栏/sticky、主按钮独占一行、vCard 折叠态仍参与生成、计数三页签/警示/阈值、示例 chip、点名、降级按钮（H→L 仍超容的第二段提示）
- [ ] 4.4 `dbx-plugin package .` 产出 candidate，提交 dbx-store candidate PR
