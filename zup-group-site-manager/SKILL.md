---
name: zup-group-site-manager
description: Manage the Jian-Ping Zou Group PowerEasy CMS website across local live-site sources and production routes. Use when auditing or changing this site’s bilingual pages, CMS nodes, search routes, mobile behavior, asset mappings, deployments, and page-by-page validation.
---

# ZJP 课题组网站管理 (zup-group-site-manager)

## 📋 概述

邹建平课题组（ZJP Group）官网：

| 组件 | 位置 | 说明 |
|------|------|------|
| **CMS 生产站** | https://jpzougroup.nchu.edu.cn | PowerEasy CMS，对外提供服务 |
| **CMS 后台** | https://jpzougroup.nchu.edu.cn/admin2019 | 节点管理、内容采编发 |
| **本地模板源** | `D:\Postgraduate\doctor\课题组网站\live-site\` | Razor 模板 + CSS + JS + 图片 |
| **静态设计基线** | `D:\Postgraduate\doctor\课题组网站\wwwroot\` | 已完成的 HTML 静态站，作为设计参考 |

## 🏠 本地目录结构（当前）

```
课题组网站/
├── live-site/           ← 唯一事实来源，待发布目录
│   ├── Views/
│   │   └── jpzougroup/
│   │       ├── Home/           # 首页模板（中英文）
│   │       ├── Pages/          # 业务页面模板（24个文件）
│   │       ├── Layout/         # 公共布局
│   │       ├── Shared/         # 网站顶部/底部/搜索
│   │       ├── ContentManage/  # CMS 内容渲染（文章/图片/单页）
│   │       ├── LetterBox/Home/ # 信箱模块（CMS默认，保留）
│   │       ├── Survey/Home/    # 问卷（CMS默认，保留）
│   │       └── Voting/Home/    # 投票（CMS默认，保留）
│   ├── Views.Phone/            # 移动端视图（镜像桌面端结构）
│   ├── content/                # 桌面端静态资源
│   │   └── jpzougroup/
│   │       ├── base/css/       # default.css module.css
│   │       ├── base/js/        # main.js（含轮播动画）
│   │       ├── home/css/       # index.css
│   │       ├── home/img/       # 5张轮播图 + 环境楼图片
│   │       └── contentmanage/css/page.css
│   ├── content.phone/          # 移动端静态资源
│   └── 重构开发文档.md          # 完整开发文档
│
├── wwwroot/             ← 静态 HTML 设计基线
│   ├── index.html
│   ├── pages/           # 分页面
│   ├── team/            # 团队成员页面
│   ├── en/              # 英文版 23 页
│   ├── css/             # 设计样式
│   ├── js/              # 交互脚本
│   └── images/          # 图片资源
│
└── .claude/skills/      # Claude 技能
```

## 📄 Pages 完整清单（24 个模板文件）

### 中文页面（17 个）

| 文件名 | 对应节点标识 | 说明 |
|--------|-------------|------|
| `研究方向.cshtml` | `yjfx` | 5个研究方向，硬编码 |
| `研究成果.cshtml` | `yjcg` | 论文列表（注意服务器过滤问题） |
| `团队动态.cshtml` | `tddt` | 新闻列表 |
| `团队活动.cshtml` | `tdhd` | 活动卡片列表 |
| `资源分享.cshtml` | `zyfx` | 常用链接和文献检索 |
| `加入我们.cshtml` | `jrwm` | 招生信息 |
| `课题组负责人.cshtml` | `ktzfzr` | 邹建平教授简历 |
| `团队成员.cshtml` | `tdcy` | 总览页，5个卡片链接 |
| `教师队伍.cshtml` | `jsdw` | 4位教师，带详情链接 |
| `博士研究生.cshtml` | `bsyjs` | 博士生列表 |
| `硕士研究生.cshtml` | `ssyjs` | 硕士生列表 |
| `毕业生.cshtml` | `bys` | 毕业生列表 |
| `课题组简介.cshtml` | `ktjj` | 课题组概况 |
| `侯冬梅.cshtml` | `hou-dongmei` | 教师详情 |
| `吴美凤.cshtml` | `wu-meifeng` | 教师详情 |
| `余建.cshtml` | `yu-jian` | 教师详情 |
| `张龙帅.cshtml` | `zhang-longshuai` | 教师详情 |

### 英文页面（7 个）

| 文件名 | 对应节点标识 |
|--------|-------------|
| `网站首页-英文.cshtml` | `en-home` |
| `研究方向-英文.cshtml` | `en-yjfx` |
| `研究成果-英文.cshtml` | `en-yjcg` |
| `团队动态-英文.cshtml` | `en-tddt` |
| `资源分享-英文.cshtml` | `en-zyfx` |
| `加入我们-英文.cshtml` | `en-jrwm` |
| `课题组负责人-英文.cshtml` | `en-ktzfzr` |
| `团队成员-英文.cshtml` | `en-tdcy` |

## 🔗 文件上传映射

```
live-site/Views/jpzougroup/...  →  服务器 /Views/jpzougroup/...
live-site/Views.Phone/jpzougroup/...  →  服务器 /Views.Phone/jpzougroup/...
live-site/content/jpzougroup/...  →  服务器 /content/jpzougroup/...
live-site/content.phone/jpzougroup/...  →  服务器 /content.phone/jpzougroup/...
```

## 🔄 模板开发工作流

```
1. 修改 live-site/ 下的 .cshtml / .css / .js 文件
2. 将文件上传到 CMS 对应目录
3. 在后台「节点管理」中绑定模板到栏目节点
4. 验证前台效果
```

## 🌐 中英文部署策略

### 英文节点创建（CMS 后台）

为每个英文页面创建对应的栏目标识：
- `en-yjfx` → 绑定 `研究方向-英文.cshtml`
- `en-yjcg` → 绑定 `研究成果-英文.cshtml`
- 以此类推...

### 节点参数设置

| 字段 | 设置 |
|------|------|
| 列表页路由规则 | `{NestedIdentifier}{?_{pageid}}`（默认） |
| 内容页路由规则 | `{NestedIdentifier}/content_{id}{?_{pageid}}`（默认） |
| 列表页模板 | 填写对应 .cshtml 路径，如 `jpzougroup/Pages/研究方向.cshtml` |
| 内容页模板 | 保持 `_Common/ContentManage/Article/内容页-文章.cshtml` |

## 📚 参考文档

完整开发文档见：`live-site/重构开发文档.md`

包含：
- 第 1-12 节：CMS 约定、栏目设计、模板职责、分阶段实施顺序
- 第 13 节：本轮模板实施记录
- 第 14 节：生产站实测问题
- 第 15 节：节点管理实施清单
- 第 16 节：中英文切换策略
- 第 17 节：HTML 复用规则
- 第 19 节：模板开发经验总结

## 🧠 开发经验总结

### 节点标识符命名规则
- **中文节点**：拼音首字母缩写。如 吴美凤→`wmf`、张龙帅→`zls`、余建→`yj`、侯冬梅→`hdm`
- **英文节点**：`en-` + 中文缩写。如 `en-wmf`、`en-zls`
- **不要用全拼**：`wu-meifeng` 不会被识别，必须用 `wmf`

### Logo 不加载的修复

**问题**：`this.Context.GetCurrentSite().LogoUrl` 在 CMS 中未配置时返回空，`<img>` 标签的 src 为空导致不显示。

**修复方案**：直接使用本地路径，不走 CMS 配置：
```csharp
var logoUrl = Url.Content(string.Format("~/content/{0}/base/img/nchu-logo.png", site.Identifier));
```
logo 实际文件位于 `content/jpzougroup/base/img/nchu-logo.png`（292×282 校徽图）。

### 论文卡片加跳转链接

**目标**：每篇论文可点击跳转到 DOI 页面。

**做法**：将 `pub-item` 从 `<div>` 改为 `<a>` 标签：
```html
<a class="pub-item" href="https://doi.org/10.1016/j.apcatb.2025.125040" target="_blank" rel="noopener">
  <span class="pub-num">1.</span>
  <div class="pub-body">...</div>
</a>
```

**CSS 配合**：`.pub-item` 需要 `text-decoration:none; color:inherit; cursor:pointer;`。

### 首页结构调整

参考 `wwwroot/index.html` 的结构顺序：
1. 轮播
2. 课题组简介 + 高亮数据 + 按钮
3. 统计数据（SCI/专利/ESI/毕业生）
4. 科研动态（时间线卡片样式）
5. 页脚（**不加**"加入我们"板块）

新闻区使用时间线卡片样式（`.news-list-home > li`），依赖 CMS `ArticleList` 输出时：
- 自动隐藏浏览量 `.hits`
- 左 accent 边框，hover 右移 4px
- 无数据时显示 fallback 示例新闻

### 手机版 Views.Phone 现状

`Views.Phone/` 和 `content.phone/` 目录仍不完整，且当前线上访问需要先通过 HTML 资源路径确认是否真的启用 Phone 模板：

- 不要仅凭本地目录存在就判断服务器已经根据 UA 切换到 Phone 模板。
- 390px 视口测试时，若线上 HTML 仍加载 `/content/jpzougroup/...`，说明当前是桌面模板的响应式适配。
- 若线上 HTML 加载 `/content.phone/...`，必须同时检查 Phone 模板、Phone CSS、Phone JS 和 Phone 图片是否成套存在。
- 选择统一响应式方案时，优先在 PC 模板和 `content/jpzougroup` 中修复；选择独立 Phone 方案时，必须补齐所有业务页面和公共脚本，不能只复制首页。

### 搜索实现方案

- **不要依赖 CMS 文章数据库搜索**：硬编码在 `.cshtml` 的内容不会自动进入 CMS ArticleList 索引。
- **采用客户端搜索**：页面嵌入 `window.SEARCH_DATA`，由通用 `search.js` 匹配标题和关键词。
- **中文入口**：`/sss` → `Views/jpzougroup/Pages/搜索.cshtml`。
- **英文入口**：`/search` → `Views/jpzougroup/Pages/搜索-英文.cshtml`。
- **查询参数统一使用 `q`**：顶部搜索框、搜索页表单、`search.js` 必须使用同一个参数名。
- **搜索页必须同时具备三件事**：`name="q"`、`search.js`、`window.SEARCH_DATA`；缺一项都会出现“搜索框能显示但没有结果”。
- **英文有效结果节点**：优先使用 `/en-sy`、`/en-yjfx`、`/en-yjcg`、`/en-tdcy/en-ktzfzr`、`/en-tdcy/en-jsdw`、`/en-tdcy/en-bsyjs`、`/en-tdcy/en-ssyjs`、`/en-tdcy/en-bys`、`/en-tddt`、`/en-tddt/en-tdhd`、`/en-zyfx`、`/en-jrwm`。
- **ContentManage 搜索模板不是默认入口**：除非节点明确绑定 `ContentManage/Search/文章搜索结果页*.cshtml`，否则修改它不会影响 `/search`。
- **Phone 搜索脚本**：如果 Phone 模板引用 `content.phone/.../base/js/search.js`，必须确认该文件实际存在；当前经验表明 Phone 目录可能缺少该脚本。
### 中英文切换注意事项
- **`VisualizationView` 区域选择**：
  - 首页 → `SiteManage/Site`
  - 内容页/业务页/搜索页 → `ContentManage/Node`
- **英文页面导航 URL**：用 `Url.Content("~/en-yjfx")` 格式（`en-` 前缀），不是 `~/en/yjfx/`
- **英文首页 URL**：通常是 `/en-sy`，通过 `ViewBag.HomeUrl ?? siteUrl + "/en-sy"` 设置
- **ViewBag.Lang 设置时机**：头部模板在页面体之前执行，需要在 header partial 的 `@{ }` 块中从 `Request.Query["lang"]` 检测语言

### 手机端首页美化要点

参考 `wwwroot/index.html` 的样式，首页每个模块按以下规则美化：

| 模块 | 美化要点 |
|------|---------|
| 轮播 | 4/5 张去掉图片纯文字，按钮在图片左右边缘 |
| 简介区 | `home-intro-grid` 双栏，手机单栏，6 个方向粗体/斜体标记 |
| 高亮数据 | `.about-highlights` 4 列，手机 2 列，白色背景卡 + 圆角 |
| 统计数据 | `.stats-grid` 4 列手机 2 列，每个 stat 加白色背景卡 |
| 新闻 | 时间线卡片：左 accent 边框 + hover 右移 + 日期块 |
| 加入我们 | 首页**不包含**此板块（参考设计没有） |

### CSS 常见陷阱
- **基类冲突**：`.cv-section h2` 有 `text-align:right`，PI 页需要覆盖为 `text-align:left`
- **`.page-banner` margin-top**：CMS 头部 `position:relative`（正常文档流），不需要 `margin-top: var(--header-total)`
- **`.pi-people-page` 不要 `overflow:hidden`**：会裁剪 grid 布局内容
- **搜索框交互**：`.site-search` 的展开/收起功能需要 `main.js` 中有对应的 JS（来自 wwwroot）

### 教师页面路由
- 教师详情页需要创建 CMS 节点，标识符用拼音首字母缩写
- 节点绑定模板路径：`jpzougroup/Pages/{中文姓名}.cshtml`
- 教师列表页的"查看简介"按钮用 `NodeUrl("{缩写}")`

### 文件上传要点
- 修改 `search.js` 后必须上传到服务器对应目录
- 所有文件的上传映射关系见上表
- `asp-append-version="true"` 确保浏览器获取最新版本

## 🧭 线上实际模板与部署验证

### 先验证渲染结果，再判断源文件

当用户报告“英文页没有结果”“改了文件但前台没有变化”时，按以下顺序操作：

1. 直接访问线上目标 URL，并记录最终 HTML。
2. 检查页面标题、表单 action、input name、当前语言和已加载脚本。
3. 根据这些特征反查实际模板，而不是默认使用用户提到的文件。
4. 对照本地 `Pages`、`ContentManage`、`Views.Phone` 三个目录的节点绑定。
5. 上传后再次读取线上 HTML，确认新模板中的唯一标志（例如 `window.SEARCH_DATA`）已经出现。

### 搜索线上验收

```powershell
$html = (Invoke-WebRequest 'https://jpzougroup.nchu.edu.cn/search?q=research' -UseBasicParsing).Content
$html -match 'SEARCH_DATA'
$html -match 'base/js/search.js'
$html -match 'Found'
```

如果线上页面仍显示旧的输入提示、没有 `SEARCH_DATA` 或没有 `search.js`，应优先处理部署和节点绑定，不要继续修改搜索匹配逻辑。

### 英文节点链接规则

英文模板文件存在不等于英文 CMS 节点已经创建。搜索数据、导航和教师按钮只能使用已经在服务器验证过的英文 URL。未绑定的英文教师详情页不要直接写入搜索结果，否则会产生 404；可以暂时指向英文 Faculty 总页。

## 🧩 内容与代码的边界

- Razor、CSS、JavaScript、资源路径和模板路由属于代码包问题。
- 节点创建、模板绑定、服务器缓存、安全过滤和内容录入属于服务器/CMS 问题。
- 论文、新闻、学生姓名、毕业去向和英文翻译属于内容问题。
- 报告问题时必须标明责任层级，避免把“本地文件已修改”误判为“线上节点已生效”。

## ✅ 更新后的完成标准

一个功能只有同时满足以下条件，才可标记为完成：

1. 本地源文件包含正确实现；
2. 实际绑定的模板是被修改的文件；
3. 所需脚本、CSS 和图片文件存在；
4. 中英文 URL 通过真实访问验证；
5. 桌面和 390px 手机版均通过交互检查；
6. 线上 HTML 能看到新模板的唯一标志；
7. 没有把服务器或内容待办隐藏在“前端完成”状态之后。