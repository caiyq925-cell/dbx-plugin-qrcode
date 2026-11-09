## 1. 页签与 i18n

- [ ] 1.1 新增第 4 个页签「解码」按钮与 `data-panel="decode"` 面板骨架（识别区 dropzone + 结果区）
- [ ] 1.2 i18n：页签名、识别区文案、结果/动作/失败提示、解码器不可用提示（中英）

## 2. 解码管线

- [ ] 2.1 `decodeFromImage(img)`：长边 >1600 等比缩放 → canvas `getImageData` → jsQR `attemptBoth` 解码
- [ ] 2.2 点击选图（file input accept 五类图片）+ FileReader/Image 管线
- [ ] 2.3 拖拽（dragover 高亮 / drop 取 files[0]）
- [ ] 2.4 全局 `paste` 监听：仅解码页签取 `image/*` 剪贴板项，不影响其他页签
- [ ] 2.5 解码器可用性：入口先 `initScanVerify()`；`failed` 时页签显示不可用提示并阻断解码

## 3. 结果区与动作

- [ ] 3.1 结果展示：可选中文本 + 类型标识（复用 `historyMetaFor` 的 kind 判断）+ 字符·字节计数
- [ ] 3.2 「复制内容」按钮（复用 `copyToClipboard` 降级管线）
- [ ] 3.3 `restoreEntry()` 反解段抽取为 `fillContent(payloadStr)`；解码「填入并生成」按类型路由并重新生成
- [ ] 3.4 失败提示（未识别 + 可能原因）；成功/失败状态行

## 4. 主流程隔离与预览

- [ ] 4.1 `generate()` 解码页签分支：payload 保持空、导出按钮禁用、自检徽标与对比度警示隐藏
- [ ] 4.2 右栏预览显示原图缩略（480px canvas contain 绘制）；空图时解码引导文案（无示例 chips）
- [ ] 4.3 解码页签隐藏历史入口（`historyBox`），切回生成页签恢复

## 5. 回归与验收

- [ ] 5.1 浏览器侧回归：三种输入方式、反色码、WiFi/vCard/网址三类回填、失败样例、切页签后主流程不受影响
- [x] 5.2 宿主侧人工验证：粘贴截图是否到达 iframe、解码器注入是否放行（用户已确认可用，2026-11-09）
- [x] 5.3 对照 specs 逐条验证；`dbx-plugin package .` 重新打包（已随 0.2.0 打包并提交 candidate PR t8y2/dbx-store#491）
