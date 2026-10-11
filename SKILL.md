---
name: "web-extract"
description: "网页正文提取 — 用 Mozilla Readability（Firefox 阅读模式同款）抓取网页，去广告/导航/侧栏，输出干净的正文。本地工具，无需 API key。"
---

# Web Extract（网页正文提取）

## 工具

CLI: `./bin/extract`

```
extract <url> [--format markdown|text] [--timeout 20000]
```

- 默认输出 markdown；`--format text` 输出纯文本
- stdout 输出 JSON：`{ title, byline, excerpt, siteName, url, redirects, length, content }`
- `url` 是跳转后的最终地址；`redirects` 是跳转链数组（`[{status, from, to}]`），无跳转时为空数组
- 失败时 stderr 输出 `{"error": "..."}` 并非零退出码（错误里同样带 `url` 和 `redirects`，方便排查死在哪一跳）

## 原理

`fetch` 抓 HTML → `jsdom` 解析 → `@mozilla/readability` 提取正文 → `turndown` 转 markdown。
Readability 是 Firefox 阅读模式用的库，专干"从一堆导航广告里抠出正文"这件事。

## 适用场景

- 知识胶囊写之前，快速抓取来源页正文核查事实
- 读长文、财报、论文页，过滤页面噪音
- 给其他任务提供干净的网页文本输入

## 局限

- 纯 fetch，不执行 JS：重度 JS 渲染的页面（如 SPA）可能抓不到内容
- 有强反爬的站（Cloudflare、36氪的火山引擎检测等）可能返回 403 或验证页
- **微信公众号文章（mp.weixin.qq.com）抓不了**：实测正文不在 HTML 里（疑似走 JS/客户端鉴权加载），换微信 UA 也一样。这种直接走浏览器任务
- 抓不到内容时换 `browser.open` 或浏览器任务；不要硬试
- 只用于公开页面；需要登录的内容不要用它绕过
