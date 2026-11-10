# 二维码工具 / QR Code Generator

A DBX workbench plugin that generates QR codes from text, Wi-Fi networks, or contact cards — with custom colors, module shapes, and a centered logo. Fully self-contained frontend plugin (no native sidecar).

一个 DBX 工作台插件：文本、Wi-Fi、名片一键生成二维码，支持自定义颜色、码点形状与中心 Logo。纯前端实现，无原生 Sidecar。

## Features / 功能

- **Text / URL** — live encoding as you type, UTF-8 support (Chinese works out of the box)
- **Wi-Fi** — SSID + password + security type (+ hidden network), generates the standard `WIFI:` payload that phones scan to join
- **Contact card** — vCard 3.0 (name, phone, email, company, title, website, note)
- **Styling** — foreground/background color, transparent background, three module shapes (square / rounded / dots), adjustable quiet zone, error-correction level, and a centered logo (forces ECC H automatically)
- **Export** — PNG at 256/512/1024 px via the host save dialog (`dbxPlugin.saveFile`), copy the image (browser environments) or the payload text (always available via `dbxPlugin.copy`)
- **History** — last 20 codes persisted through `host.storage` (localStorage fallback outside the host); click an entry to restore its content *and* style
- **Theme & locale** — follows the host light/dark theme and zh-CN/en locale

QR encoding uses [Nayuki's QR-Code-generator](https://github.com/nayuki/QR-Code-generator) (MIT, inlined); all rendering is custom Canvas drawing.

## Project layout

```
dbx-plugin-qrcode/
├── manifest.json        # manifest v1, permission: host.storage
├── dbx-plugin.toml      # package config (stages assets/ + ui/)
├── assets/plugin.svg    # plugin/workbench icon
└── ui/index.html        # the whole UI: inline qrcodegen lib + inline app + icon
```

The UI must stay **self-contained**: the DBX host loads `ui/index.html` into a sandboxed `srcdoc` iframe, so everything (library, icon) is inlined — no external `src`/`href` references.

## Develop

```bash
dbx-plugin dev --path . --port 5190
```

No backend or DBX application is required. Open the workbench from the development shell at `http://127.0.0.1:5190/`. `.dbx-dev/` stores local dev data and must not be committed or packaged.

## Package & release automation

```bash
dbx-plugin package .
```

Writes an unsigned universal `.dbxp` candidate plus `.artifact.json` into `dist/`.

To have the DBX Store automatically open/update the candidate PR for a release, publish a GitHub Release with **three** assets:

1. the unsigned `.dbxp` candidate (e.g. `com.caiyq925.qrcode-0.2.0-universal.dbxp`);
2. `release-candidates.json` — the plugin identity plus the packaged artifact, exactly what `dbx-plugin package .` prints in `dist/*.artifact.json`, wrapped with the manifest identity:

   ```json
   {
     "plugin": { "id": "com.caiyq925.qrcode", "publisher": "caiyq925", "version": "0.2.0" },
     "artifacts": [
       { "target": "universal", "url": "com.caiyq925.qrcode-0.2.0-universal.dbxp", "sha256": "<64 hex>", "size": 89634 }
     ]
   }
   ```

3. (optional) any extra release notes assets.

The store synchronizer polls repos registered in `automation/plugin-sources.json` (`autoUpdate: true`), reads the latest release's `release-candidates.json`, and opens/updates the candidate PR. Listing metadata (name/description/icon/localizations) comes from `.dbx-store.json` at the release tag.

## Submit to the marketplace

See [CONTRIBUTING](https://github.com/t8y2/dbx-store/blob/main/CONTRIBUTING.md) in `t8y2/dbx-store`: publish a GitHub Release with the unsigned candidate, then open one candidate PR against `t8y2/dbx-store:main` (plus `publishers/caiyq925.json` on first submission). Maintainers review and the protected workflow signs the artifact.
