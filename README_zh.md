# web-extract

用 Mozilla Readability（Firefox 阅读模式同款库）提取网页正文，去广告、导航、侧栏，输出干净的正文。本地工具，无需 API key。

[English](README.md)

## 安装

```bash
npm install
```

## 用法

```bash
./bin/extract <url> [--format markdown|text] [--timeout 20000]
```

stdout 输出 JSON：`{ title, byline, excerpt, siteName, url, redirects, length, content }`

- 默认输出 markdown；`--format text` 输出纯文本
- `url` 是跳转后的最终地址；`redirects` 是跳转链数组，无跳转时为空
- 失败时 stderr 输出 `{"error": "..."}` 并非零退出码

## 原理

`fetch` 抓 HTML → `jsdom` 解析 → `@mozilla/readability` 提取正文 → `turndown` 转 markdown。

## 适用场景

- 写文章前快速抓取来源页正文核查事实
- 读长文、财报、论文页，过滤页面噪音
- 给其他任务提供干净的网页文本输入

## 局限

- 纯 fetch，不执行 JS：重度 JS 渲染的页面（如 SPA）可能抓不到内容
- 有强反爬的站（Cloudflare 等）可能返回 403
- **微信公众号文章（mp.weixin.qq.com）抓不了**：正文不在 HTML 里，这种走浏览器

## 许可证

MIT
