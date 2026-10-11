# web-extract

[English](#english) | [中文](#中文)

Extract clean article text from web pages using Mozilla Readability (the same engine behind Firefox Reader View).

用 Mozilla Readability（Firefox 阅读模式同款引擎）从网页中提取干净的文章正文。

---

## <a id="english"></a>English

### Install

```bash
# Requires Node.js 18+
git clone https://github.com/nailong-dawang/web-extract.git
cd web-extract
npm install
```

### Usage

```bash
node bin/extract <url> [--format markdown|text] [--timeout 20000]

# Examples
node bin/extract "https://example.com/article"
node bin/extract "https://example.com/article" --format text
node bin/extract "https://example.com/article" --timeout 30000
```

### Output

Prints JSON to stdout:

```json
{
  "title": "Article Title",
  "byline": "Author Name",
  "excerpt": "Summary...",
  "siteName": "example.com",
  "url": "https://example.com/article",      // final URL after redirects
  "redirects": [{"status": 301, "from": "...", "to": "..."}],
  "length": 5432,
  "content": "# Article Title\n\nFull text in markdown..."
}
```

- Default format is `markdown`; use `--format text` for plain text
- `url` is the final address after following redirects; `redirects` is the full chain (empty if none)
- On failure, stderr gets `{"error": "...", "url": "...", "redirects": [...]}` with a non-zero exit code

### How it works

```
fetch HTML → jsdom parse → @mozilla/readability extract → turndown to markdown
```

Readability is the library Firefox Reader View uses — it specializes in pulling the article body out of navigation, ads, and sidebars.

### Limitations

- **No JS execution**: pure `fetch`, so heavily JS-rendered pages (SPAs) may return nothing
- **Anti-bot sites**: Cloudflare and similar protections may return 403 or a challenge page
- **WeChat articles** (`mp.weixin.qq.com`) don't work — the body isn't in the HTML (loaded via JS/client auth)
- **Login-walled content**: don't use this to bypass authentication
- When extraction fails, try a real browser instead of retrying

---

## <a id="中文"></a>中文

### 安装

```bash
# 需要 Node.js 18+
git clone https://github.com/nailong-dawang/web-extract.git
cd web-extract
npm install
```

### 用法

```bash
node bin/extract <url> [--format markdown|text] [--timeout 20000]

# 示例
node bin/extract "https://example.com/article"
node bin/extract "https://example.com/article" --format text
node bin/extract "https://example.com/article" --timeout 30000
```

### 输出

向 stdout 输出 JSON：

```json
{
  "title": "文章标题",
  "byline": "作者",
  "excerpt": "摘要...",
  "siteName": "example.com",
  "url": "https://example.com/article",      // 跳转后的最终地址
  "redirects": [{"status": 301, "from": "...", "to": "..."}],
  "length": 5432,
  "content": "# 文章标题\n\nmarkdown 格式的全文..."
}
```

- 默认输出 markdown；`--format text` 输出纯文本
- `url` 是跟随跳转后的最终地址；`redirects` 是完整跳转链（无跳转时为空数组）
- 失败时 stderr 输出 `{"error": "...", "url": "...", "redirects": [...]}` 并以非零退出码退出

### 原理

```
fetch 抓 HTML → jsdom 解析 → @mozilla/readability 提取正文 → turndown 转 markdown
```

Readability 是 Firefox 阅读模式用的库，专干"从一堆导航广告里抠出正文"这件事。

### 局限

- **不执行 JS**：纯 `fetch`，重度 JS 渲染的页面（如 SPA）可能抓不到内容
- **反爬站点**：Cloudflare 等防护可能返回 403 或验证页
- **微信公众号文章**（mp.weixin.qq.com）抓不了：实测正文不在 HTML 里（疑似走 JS/客户端鉴权加载）
- **需要登录的内容**：不要用它绕过鉴权
- 抓不到时换真实浏览器，不要硬试

## License

MIT
