## Context

P0-P2 与密度调整已落地，插件为双栏布局、四页签前的三页签结构。内联 jsQR（`jsqrLib` text/plain，惰性注入）目前仅服务自检徽章；`restoreEntry()` 内含 WiFi/vCard 载荷反解回填逻辑。本 change 新增「解码」页签，全部能力本地完成。

## Goals / Non-Goals

**Goals:**
- 选图/拖拽/粘贴三种输入，本地解码，结果可复制、可反解回填生成
- 与生成主流程完全隔离（历史/样式/导出不受影响）

**Non-Goals:**
- 不做多码识别（jsQR 单帧单码）
- 不做摄像头扫码（桌面插件场景，截图粘贴已覆盖主诉求）
- 解码结果不入历史（查询行为，非生成记录，维持显式保存语义）

## Decisions

1. **复用 `jsqrLib` 与 `initScanVerify()` 的注入管线，不复制第二份解码器**
   解码入口先 `initScanVerify()`：`ready` 则直接用 `window.jsQR`；`failed` 则页签显示不可用提示（不报错）。单文件只有一份解码器源，体积零增量。

2. **解码用 `inversionAttempts: "attemptBoth"`，图片长边 >1600px 先等比缩放**
   `attemptBoth` 覆盖反色码（深底浅码），代价仅双倍耗时（单帧 <100ms）；1600px 上限防超大图撑爆 canvas 内存。截图/屏拍常见尺寸不受影响。

3. **结果区放左栏面板内，右栏预览显示原图缩略**
   「右栏 = 所见」语义保持：生成页签右栏是码，解码页签右栏是你正在解的图。原图用既有 480px 预览 canvas 绘制（等比 contain），空图时显示解码引导文案（无示例 chips——chips 是生成专属）。

4. **`restoreEntry()` 反解段抽取为 `fillContent(payloadStr)`，生成恢复与解码回填共用**
   restoreEntry = 样式恢复 + fillContent；解码回填 = fillContent（不动样式）。WiFi/vCard 正则反解逻辑单点维护。

5. **粘贴用全局 `document` `paste` 监听，仅解码页签消费图片项**
   `clipboardData.items` 中 `kind==="file"` 且 type 以 `image/` 开头才取；其他页签/文本粘贴直接 return，不影响 textarea 行为。宿主 iframe 焦点问题（粘贴是否到达 iframe）标注为宿主侧人工验证项。

6. **识别区（dropzone）用 `role="button"` + `tabindex=0` + Enter/Space 触发**
   与 a11y change 的键盘可操作性基线一致；拖拽高亮态用 `dragover` class 切换。

## Risks / Trade-offs

- [宿主沙箱拦截动态 script 注入导致 jsQR 不可用] → 解码页签整体降级提示（与自检徽标同一降级路径），生成功能零影响
- [粘贴焦点问题：宿主中 iframe 未聚焦时收不到 paste] → 三输入方式互为冗余（选图/拖拽必可达）；粘贴列为宿主人工验证项
- [屏拍/模糊图识别率低] → 失败文案给出可能原因引导重拍；不做图像增强（超出本地工具定位）
- [超大 GIF/动图] → 取首帧绘制 canvas 解码，动图不逐帧扫描

## Migration Plan

随版本发布，纯前端新增；无存储、无 schema 变更。回滚 = 回退插件包。

## Open Questions

- 宿主内 `Ctrl+V` 粘贴图片能否到达沙箱 iframe（待人工验证；选图/拖拽不受影响）
