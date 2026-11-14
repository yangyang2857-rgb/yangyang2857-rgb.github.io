# 王孙阳 · 个人主页

[English](README.md) · **简体中文**

纯手写 HTML / CSS / JavaScript 搭起来的个人站点，没有框架、没有构建、没有第三方依赖。改完文件 `git push`，GitHub Pages 自动发布。

**线上地址：<https://yangyang2857-rgb.github.io/>**

![GitHub Pages](https://img.shields.io/badge/hosting-GitHub%20Pages-2ea44f)
![No build](https://img.shields.io/badge/build-none-blue)
![No deps](https://img.shields.io/badge/dependencies-0-lightgrey)
![Theme](https://img.shields.io/badge/theme-dark%20%2F%20light-5eead4)

## 目录

- [仓库里有什么](#仓库里有什么)
- [页面一览](#页面一览)
- [技术实现](#技术实现)
- [本地预览](#本地预览)
- [写一篇新笔记](#写一篇新笔记)
- [部署方式](#部署方式)
- [命名与路径约定](#命名与路径约定)
- [浏览器兼容与降级](#浏览器兼容与降级)
- [已知取舍](#已知取舍)
- [联系](#联系)
- [版权](#版权)

## 仓库里有什么

| 路径 | 作用 | 说明 |
| --- | --- | --- |
| `index.html` | 首页 | 单页长滚动，八个区块：首屏、关于、现在、技能、作品、实验室、成长轨迹、联系 |
| `blog.html` | 笔记列表 | 卡片列表 + 分类筛选（全部 / 前端 / 效率），空分类有兜底提示 |
| `404.html` | 404 页 | GitHub Pages 会自动把它用于未命中的路径 |
| `posts/handwritten-html.html` | 第 1 篇 | 《为什么我用纯手写 HTML 做个人主页》，2026-11-08 |
| `posts/three-habits.html` | 第 2 篇 | 《把重复的事交给工具：三个小习惯》，2026-10-21 |
| `笔记更新指南.md` | 维护文档 | 加新文章、改分类、改上一篇/下一篇的完整步骤 |

整个仓库只有 7 个文件、约 140 KB，没有 `package.json`、没有 `node_modules`、没有构建产物，克隆下来直接就是发布内容。

## 页面一览

**首页 `index.html`**

- 首屏：姓名、定位（在读学生 · 前端 / 设计 / AI 工具）、两个入口按钮（作品 / 笔记）
- 关于：自我介绍与两个数字（个人项目、常用工具）
- 现在：在学 / 在做 / 在看三栏，比简历更即时
- 技能：网页开发、设计与排版、内容创作、AI 工具应用，只写「具体能做什么」，不打分数
- 作品：个人主页、学习笔记、效率工具小实验三张卡片
- 实验室：批量重命名与归档、资料汇总成索引、写作先要结构——三个日常在跑的小流程
- 成长轨迹：时间轴，从「好奇网站怎么做出来」到「探索与积累」
- 联系：邮箱、微信、GitHub

**笔记页 `posts/*.html`**

每篇文章是独立 HTML，结构一致：`<title>` + `meta description` + `article:published_time`、`.art-meta`（分类 · 日期 · 阅读时长）、`<h1>`、`.toc` 本页目录（窄屏收成抽屉）、`.prose` 正文、`.post-nav` 上一篇 / 下一篇。

## 技术实现

### 没有构建步骤

选择手写而不是 Jekyll / Hexo / Vite，原因很直接：这个站点只有几页，引入构建链后每次改一行字都要装环境、跑命令、等产物。现在的做法是「文件即站点」，编辑器里改完推送就上线，任何人查看源代码看到的就是实际运行的代码。

代价是样式在四个页面里各有一份内联副本（首页、列表、404、文章模板），改配色要改四处——这是刻意接受的重复，换来零依赖和零等待。

### 双主题

- 主题由 `<html data-theme="dark|light">` 控制，两套 CSS 变量：深色底 `#060a14`、浅色底 `#f6f7fb`，强调色分别是 `#5eead4` 与 `#0e9887`
- 用户点右上角按钮切换，选择写进 `localStorage`；没手动选过时跟随系统 `prefers-color-scheme` 并监听其变化
- 关键细节：`<head>` 里内联了一段同步脚本，**在首帧绘制前**就把 `data-theme` 定好，避免深色用户看到一闪而过的白屏
- 切换时同步更新 `meta[name="theme-color"]`，手机浏览器地址栏颜色跟着变

### 排版与响应式

- 字体走系统栈：`-apple-system` / `Segoe UI` / `PingFang SC` / `HarmonyOS Sans SC` / `Hiragino Sans GB` / `Microsoft YaHei`，不加载任何 Web 字体，中文首屏没有 FOUT
- 标题与间距用 `clamp()` 做流体缩放（如 `clamp(56px, 9vw, 82px)`），不靠一堆断点硬撑
- 网格在三档断点（920 / 820 / 620px）收窄：三列 → 两列 → 单列，首屏双栏在小屏改为上下堆叠

### 交互

- 顶部滚动进度条，`requestAnimationFrame` 节流，一帧最多算一次
- 区块用 `IntersectionObserver` 做进入视口时的淡入上移，不监听 `scroll` 算位置
- 笔记列表的分类筛选纯前端实现，按 `data-cat` 匹配，切换时同步 `aria-pressed` 并显示/隐藏空状态提示

### 可访问性

- 每个页面第一个可聚焦元素是「跳到主要内容」链接，直达 `#main`
- 导航、文章目录、上一篇/下一篇都带 `aria-label`；装饰性箭头、分隔符标 `aria-hidden="true"`
- 主题按钮用 `aria-pressed` 表达当前状态，并按目标状态改写 `aria-label`（「切换到浅色模式」/「切换到深色模式」）
- `prefers-reduced-motion: reduce` 下关闭淡入、平滑滚动等动效
- 语义化结构：一个 `<h1>`、区块用 `<section>` + `<h2>`，正文用 `<p>` / `<ul>` / `<pre><code>`

### 分享与收录

- 每页有 `canonical`、`description`、`og:*`、`twitter:card`
- favicon 是内联 `data:` 的 SVG（深色圆角方块 + 「王」字），不额外发请求
- `meta name="color-scheme"` 声明支持深浅色，让表单控件和滚动条跟随主题

## 本地预览

因为页面之间是相对路径，直接双击打开也能看，但更推荐起一个本地服务，避免 `file://` 下的路径与 `localStorage` 行为差异：

```bash
git clone https://github.com/yangyang2857-rgb/yangyang2857-rgb.github.io.git
cd yangyang2857-rgb.github.io
python3 -m http.server 8000
# 浏览器打开 http://localhost:8000/
```

## 写一篇新笔记

完整步骤见 [`笔记更新指南.md`](笔记更新指南.md)，这里是要点：

1. 复制 `posts/three-habits.html` 作为模板，重命名为英文小写短横线（如 `css-layout-notes.html`）
2. 改六处：`<title>`、`meta description`、`article:published_time`、`.art-meta`、`<h1>`、`.prose` 正文
3. 改底部 `.post-nav`：左边指向前一篇，右边指向后一篇，没有就留 `<div class="empty-slot">`
4. 在 `blog.html` 的 `<ul class="post-list">` **最上面**插入新卡片（最新的在最上），同步筛选按钮上的 `count` 数字；开新分类才需要加筛选按钮
5. 推送，等 Pages 构建完成

## 部署方式

- 托管：GitHub Pages，源为 `main` 分支根目录
- 域名：`yangyang2857-rgb.github.io`，没有 `CNAME` 文件，也没有自定义域
- 没有 GitHub Actions 工作流：不需要构建，Pages 只负责把文件原样发出去
- 发布延迟：推送后通常几十秒内生效，缓存未过期时强刷一次（Ctrl/Cmd + Shift + R）

## 命名与路径约定

| 约定 | 说明 |
| --- | --- |
| 文章文件名 | 英文小写 + 短横线。中文名可用，但网址会变成一长串 `%E4%B8%AD%E6%96%87`，不好分享 |
| 相对路径 | `posts/` 内互相链接**不加** `../`；指回主页或列表页才写 `../index.html`、`../blog.html` |
| 分类一致性 | 卡片的 `data-cat` 与筛选按钮的 `data-filter` 必须完全相同（含空格），差一个字符筛出来就是空的 |
| 日期 | 文章页的 `article:published_time` 与列表页的 `<time datetime>` 两处都要改 |
| 代码块转义 | 正文里出现 `<` `>` 要写成 `&lt;` `&gt;`，否则标签会被浏览器吃掉 |

## 浏览器兼容与降级

面向近两年的主流浏览器（Chrome / Edge / Safari / Firefox，含移动端）。用到的都是稳定特性：CSS 变量、Grid、`clamp()`、`IntersectionObserver`。

做过的降级处理：

- `localStorage` 读写全部包在 `try/catch` 里——隐私模式下读写会抛异常，主题退回跟随系统，不影响浏览
- `matchMedia(...).addEventListener` 不可用时退回 `addListener`
- `prefers-reduced-motion` 生效时，动效全部关掉，只保留静态样式

## 已知取舍

- **样式四份重复**：首页、列表、404、文章各有一份内联 CSS。改配色或间距要同步四处，好处是零构建、零请求
- **文章靠手动维护**：加文章 = 复制模板 + 改内容 + 列表页加卡片。文章到二十篇以上再考虑写个本地脚本批量生成
- **没有 sitemap / RSS**：目前靠搜索引擎自然收录，没有主动提交
- **没有暗色图片适配**：站内没用到照片，如果以后加，需要按主题准备两套或加滤镜

## 联系

| 渠道 | 地址 |
| --- | --- |
| 邮箱 | [3383927177@qq.com](mailto:3383927177@qq.com) |
| 微信 | `ILOVEHUTAOMUCH` |
| GitHub | [@yangyang2857-rgb](https://github.com/yangyang2857-rgb) |

## 版权

仓库没有附 `LICENSE` 文件，代码与文字著作权归作者所有。想转载文章或复用页面设计，先邮件或微信说一声。
