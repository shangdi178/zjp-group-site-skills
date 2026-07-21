---
name: powereasy-template-adapter
description: Adapt, debug, validate, and deploy PowerEasy CMS Razor templates, CSS, JavaScript, bilingual routes, client-side search, responsive mobile layouts, and server-rendered template mappings. Use when working on PowerEasy/WebFuture .cshtml templates, live-site assets, CMS node bindings, template security errors, or verifying that local changes reached the production-rendered page.
---

# PowerEasy CMS 模版适配指南 (powereasy-template-adapter)

## 🎯 目标

将新设计的 CSS 样式和 Razor 模板应用到 PowerEasy CMS 网站。

## ⚡ 核心原则

**先改本地 `live-site/` 文件，再上传到 CMS。本地是唯一源头。**

```
本地修改 → 上传 CMS → 验证前台
  ↓            ↓          ↓
live-site/ → Views/ + content/ → 刷新页面验证
```

## 📐 CSS 覆写法（层叠覆盖）

**不替换模版 HTML，而是通过 CSS 选择器覆写 CMS 原生 HTML 结构。**

CMS 前台 HTML 结构固定为：
```
#header → .siteWidth (grid: logo | title | search)
         → .mainNav (flex 导航栏)
#indBanner (首页轮播)
.boxA → .hd (标题) → .bd (内容)
#footer
```

### 文件路径映射

| 本地 (live-site/) | CMS |
|---|---|
| `content/jpzougroup/base/css/default.css` | `content/jpzougroup/base/css/default.css` |
| `content/jpzougroup/base/css/module.css` | `content/jpzougroup/base/css/module.css` |
| `content/jpzougroup/home/css/index.css` | `content/jpzougroup/home/css/index.css` |

### 设计变量

```
--primary: #1a3a5c        primary-dark: #0f2440
--accent: #5e6ad2         --bg-light: #f6f7f8
--text-body: #3e4044      body font-size: 17px
```

## 🏗️ Razor 模板开发规则

### 1. 每个页面必须加 VisualizationView

```
@Power.VisualizationView(new { Area = "ContentManage", Controller = "Node" })
```

- 内容页/列表页用 `Area = "ContentManage", Controller = "Node"`
- 首页用 `Area = "SiteManage", Controller = "Site"`
- Shared 下的局部模板用 `@Power.VisualizationPartialView`
- **不加此指令会报错，且无法可视化编辑**

### 2. 页面必须继承公共布局

```
Layout = $"~/Views/{this.Context.GetCurrentSite().Identifier}/Layout/公共布局页.cshtml"
```

### 3. 内容策略

- **静态模式**：内容直接硬编码在 .cshtml 中，不依赖 CMS 后台录入
- **CMS 驱动模式**：使用 `@Power.ArticleList`、`@Power.Partial("节点配置信息")` 读取后台内容

### 4. 社区团队子页面模板（必读）

所有团队成员页面（教师队伍、博/硕士研究生、毕业生）需要以下结构：

```
Page.cshtml → banner + member-tabs + member-grid
```

每个页面需包含 `member-tabs` 导航栏（5个tab互相链接），用 `@Power.Url.NodeUrl()` 生成链接。

### 5. ContentManage 模板

`Views/jpzougroup/ContentManage/` 目录只保留以下文件，其余已删除：

| 保留文件 | 用途 |
|---|---|
| `Article/文章-内容页.cshtml` | 文章/新闻详情阅读页 |
| `Article/文章-子列表页.cshtml` | 通用文章列表 |
| `Article/文章-子列表页-图片式.cshtml` | 带头像卡片列表 |
| `Other/单页.cshtml` | 单页内容展示 |
| `Search/文章搜索结果页.cshtml` | CMS 通用搜索结果模板（只有节点绑定该模板时才生效） |
| `Photo/图片-内容页.cshtml` | 图片查看 |
| `Photo/图片-子列表页.cshtml` | 图片列表 |
| `Photo/图片-父列表页.cshtml` | 图片父页 |

## 🌐 双语实现

### 语言识别

```
英文页面：ViewBag.Lang = "en"
中文页面：不设置（默认走中文分支）
```

### 语言切换（网站顶部.cshtml）

```
@if (ViewBag.Lang == "en")
    → 显示：中文(链接)  EN(高亮)
    → 导航：Home / Research / Publications / People / News / Resources / Join Us
@else
    → 显示：中文(高亮)  EN(链接)
    → 导航：首页 / 研究方向 / 研究成果 / 团队成员 / 团队动态 / 资源分享 / 加入我们
```

### 对应链接

```
中文页：ViewBag.LangEn = NodeUrl("en-yjfx")   指向英文版
英文页：ViewBag.LangZh = NodeUrl("yjfx")      指向中文版
```

### 页脚（网站底部.cshtml）

通过 `var isEn = ViewBag.Lang == "en"` 判断语言，切换中英文地址、链接文字、版权信息。

### 搜索框（网站搜索.cshtml）

placeholder 和 alert 提示文字通过 `ViewBag.Lang` 切换。

## 📱 手机端响应式改造

### 1. 架构选择：统一响应式（推荐）

```
方案A（推荐）：桌面 + 手机共用同一套 Razor 模板 + CSS，通过媒体查询适配
方案B：服务器根据 UA 切换 Views.Phone
```

**当前线上实际使用方案 A**。`Views.Phone` 目录存在但不完整（缺少 `_Common/Layout/基础布局页.cshtml`），服务器未启用手机 UA 路由。新功能直接在 PC 模板的 CSS 中加 `@media (max-width:820px)` 断点即可。

### 2. 手机端头部改造

**目标**：桌面导航 → 手机端汉堡菜单 + 抽屉导航

**HTML 结构**（`网站顶部.cshtml`）：
- 在 `.header-tools` 末尾加 `<button class="nav-toggle" id="navToggle">`（三个 span 做汉堡图标）
- header 后面加 `.drawer-overlay` + `.drawer-nav`（含 drawer-header/drawer-lang/drawer-menu）
- drawer-menu 中的二级菜单用 `.drawer-menu-item > a[onclick]` + `.drawer-sub` 实现展开收起
- 语言切换在 drawer 内重复一份（`.drawer-lang`）

**CSS 关键规则**：
```css
@media (max-width:820px) {
  #header #mainNav { display:none; }           /* 隐藏桌面导航 */
  .nav-toggle { display:flex; }                 /* 显示汉堡按钮 */
  #header .lang-toggle { display:flex; }        /* 语言切换保持可见（重要：删除旧的 display:none） */
}
```

**抽屉导航 CSS**（`default.css`）：
- `.drawer-overlay`：固定全屏半透明遮罩，z-index:999
- `.drawer-nav`：固定右侧，`width:min(320px,80vw)`，`transform:translateX(100%)` 控制滑入/滑出
- `.drawer-menu-item.open .drawer-sub`：二级菜单展开
- `body.drawer-open { overflow:hidden; }`：抽屉打开时锁定滚动

**JS 逻辑**（`main.js`）：
```javascript
// 打开/关闭
toggle.addEventListener('click', openDrawer);
closeBtn.addEventListener('click', closeDrawer);
overlay.addEventListener('click', closeDrawer);
document.addEventListener('keydown', e => { if(e.key === 'Escape') closeDrawer(); });
// 二级菜单折叠
drawer.querySelectorAll('.drawer-menu-item > a[onclick]').forEach(a => {
  a.addEventListener('click', e => { e.preventDefault(); parent.classList.toggle('open'); });
});
```

### 3. 手机端标题栏紧凑布局

**常见问题**：标题文字与导航重叠、超行溢出

**解决方案**：
```css
@media (max-width:820px) {
  #header { overflow:hidden; }
  #header .header-top-row { gap:0; min-height:44px; padding:4px 6px; }
  #header .logo img { height:28px; max-width:80px; }
  #header .header-site-title { flex:1; overflow:hidden; }
  #header .header-site-title .title-cn {
    font-size:.8rem; white-space:nowrap; overflow:hidden;
    text-overflow:ellipsis; line-height:1.1;
  }
  #header .header-site-title .title-en { display:none; }  /* 手机端隐藏副标题省空间 */
}
```
**核心思路**：Logo 缩小、标题左对齐、英文副标题隐藏、工具按钮缩小、`overflow:hidden` 防溢出。

### 4. 滚动进场动画模式

**不要用内联 style 控制动画状态**，改用 CSS 类管理：

```css
/* 默认可见（JS 加载失败也不影响） */
.animate-item { opacity:1; transform:none; }
/* JS 可用时才隐藏 */
.js-enabled .animate-item.will-animate { opacity:0; transform:translateY(16px); transition:opacity .5s, transform .5s; }
.js-enabled .animate-item.is-visible { opacity:1; transform:none; }
```

```javascript
// JS 侧
document.documentElement.classList.add('js-enabled');
// 首屏元素立即显示
animateEls.forEach(el => {
  if (el.getBoundingClientRect().top < window.innerHeight) {
    el.classList.add('is-visible');
  }
});
// 超时保护 1.5s
setTimeout(() => { document.querySelectorAll('.will-animate').forEach(el => {
  el.classList.remove('will-animate'); el.classList.add('is-visible');
})}, 1500);
```

**验收标准**：
- 禁用 JS 后所有内容仍可见
- `prefers-reduced-motion: reduce` 时无动画
- 快速滚动不留透明卡片

### 5. 手机端轮播优化

```css
@media (max-width:820px) {
  .hero-slider { aspect-ratio:16/10; }
  .slide-text { left:5%; bottom:12%; max-width:75%; }
  .slide-text h2 { font-size:1.15rem; }
  .slide-text p { font-size:.72rem; max-height:2.6em; }
  /* 按钮移到图片边缘中部，远离文字区 */
  .slider-btn { top:50%; width:30px; height:30px; }
  .slider-btn.prev { left:4px; }
  .slider-btn.next { right:4px; }
}
```

JS 增加触摸滑动：
```javascript
slider.addEventListener('touchstart', e => { touchStartX = e.changedTouches[0].screenX; stopSlider(); });
slider.addEventListener('touchend', e => {
  const diff = touchStartX - e.changedTouches[0].screenX;
  if (Math.abs(diff) > 50) { diff > 0 ? nextSlide() : prevSlide(); }
  startSlider();
});
// 页面进入后台暂停
document.addEventListener('visibilitychange', () => { document.hidden ? stopSlider() : startSlider(); });
```

### 6. 响应式断点策略

| 断点 | 适用场景 |
|------|---------|
| `<=360px` | 极小屏手机，进一步缩小间距/字号，成员卡片单列 |
| `<=640px` | PI 页头像单列 |
| `<=768px` | CV 双栏改单栏、时间线调整 |
| `<=820px` | **主要手机断点**：汉堡菜单、卡片单列、统计 2 列 |
| `768-820px` | 平板微调（成员卡片 3 列） |

### 7. CSS 变量统一管理

```css
:root {
  --radius-sm: 6px; --radius-md: 10px; --radius-lg: 14px;
  --font-base: 17px;
}
@media (max-width:820px) {
  :root { --radius-md: 8px; --radius-lg: 12px; --font-base: 16px; }
}
```
所有卡片、按钮、输入框统一引用变量，修改一处全局生效。

## ⚠️ 已知问题

### 1. 服务端安全过滤

部分内容会触发 CMS "非法内容" 报错：
- **特殊 Unicode 字符**：不间断连字符 `U+2011`、特殊空格 → 用标准 ASCII 替代
- **内容长度**：过长的硬编码列表可能被拦截 → 建议单文件控制合理长度
- **HTML 标签**：`<em>`、`<strong>`、`<sub>`、`<sup>` 在部分场景下被过滤 → 纯文本更安全

### 2. 研究成果页面

由于服务端过滤，`研究成果.cshtml` 目前只能展示 1 篇论文。后续尝试增量添加解决。

## 📂 节点管理

### 栏目标识约定

```
中文栏目标识：yjfx yjcg tdcy tddt zyfx jrwm ktzfzr jsdw bsyjs ssyjs bys
英文栏目标识：en-yjfx en-yjcg en-tdcy en-tddt en-zyfx en-jrwm en-ktzfzr en-jsdw ...
教师详情标识：hou-dongmei wu-meifeng yu-jian zhang-longshuai
```

### 路由规则

保持默认即可：
- 列表页：`{NestedIdentifier}{?_{pageid}}`
- 内容页：`{NestedIdentifier}/content_{id}{?_{pageid}}`

## 📐 团队成员详情页模板（教师/学生/毕业生）

所有团队成员详情页（侯冬梅、吴美凤、张龙帅、余建等）应统一使用与 PI 页相同的布局框架：

```
pi-people-page          → 最外层卡片容器
  pi-profile-head       → 头像 + 姓名职称 + 联系方式 + 个人简介
    pi-portrait        → 头像图片（200×267 比例）
    pi-name-block      → 姓名 + 职称
    pi-meta-list        → 邮箱、研究方向等
    pi-intro-block     → 个人简介
  pi-cv-content         → 教育经历 / 工作经历 / 科研项目（纯文本列表）
```

### 头像规则

- 有真实照片：用 `<img>` 标签，图片放 `content/.../home/img/`
- 无照片：用姓名字母占位（div + background gradient + 首字），保持方形 `border-radius:var(--radius-md)`
- 尺寸桌面 130×160px，手机 110×135px

### 教师队伍卡片规则

整张卡片是 `<a>` 链接，点击跳转到个人详情页：

```html
<a class="member-card" href="@Power.Url.NodeUrl("wmf")">
  <div class="avatar"><img src="..." alt="..." /></div>
  <h3>吴美凤</h3><p class="role">副教授</p>
</a>
```

- 头像方形（`border-radius:var(--radius-md)`），不用圆形
- 卡片内不含研究方向和邮箱（精简）
- 手机端头像 110×135px，桌面 130×160px

### member-tabs 居中导航

```css
.member-tabs {
  display:flex; justify-content:center; gap:6px;
  padding:20px 20px 0; flex-wrap:wrap;
  max-width:var(--max-width); margin:0 auto;
}
```
必须设置 `max-width + margin:0 auto` 才能在页面中居中。

## 🧭 导航高亮 JS 模式

### 职责
`main.js` 中的导航高亮逻辑负责为当前页面所在的导航项添加 `.on1` 类。

### 标准实现

```javascript
var items = document.querySelectorAll('.mainNav .li1, .drawer-menu-item');
var cur = window.location.pathname.replace(/\/$/, '') || '/';
items.forEach(function(li) {
  var a = li.querySelector('a:not([onclick])');
  if (!a) return;
  var href = a.getAttribute('href');
  if (!href) return;
  // 提取路径部分：完整URL只取路径名
  try { var url = new URL(href); href = url.pathname; } catch(e) {}
  var hrefClean = href.replace(/\/$/, '') || '/';
  var curClean = cur.replace(/\/$/, '') || '/';
  // 精确匹配，或子路径匹配父导航（如 /tdcy/en-ktzfzr 匹配 /tdcy）
  if (hrefClean === curClean || (hrefClean.length > 1 && curClean.indexOf(hrefClean + '/') === 0)) {
    li.classList.add('on1');
  }
});
```

### 常见错误

| 错误 | 后果 | 正确做法 |
|------|------|---------|
| `cur.indexOf(href)` 不做长度保护 | 根路径 `/` 匹配所有导航项 | 加 `hrefClean.length > 1` 过滤 |
| 直接用 `href` 不做 URL 解析 | 完整 URL(https://...) 和路径名无法匹配 | 用 `new URL()` 提取 `.pathname` |
| 只用 `active` 不用 `on1` | CSS 用 `.on1` 但 JS 加的是 `active` | 两个都加，或统一使用 CSS 类名 |

## 🎬 CSS 过渡动效添加规则

### 基本原则
- `transition` 属性必须写在**基础规则**（非 hover）中
- 如果媒体查询覆盖了基础规则，**必须同时保留 `transition` 属性**

```css
/* ✅ 正确：transition 在基础规则中 */
.mainNav .li1 .a1 {
  transition: background .3s ease, color .3s ease;
}
.mainNav .li1 .a1:hover {
  background: rgba(255,255,255,0.18);
}

/* ❌ 错误：手机覆盖丢失 transition */
@media (...) {
  .mainNav .li1 .a1 { font-size:.82rem; }  /* 这里丢了 transition */
}

/* ✅ 正确：保留 transition */
@media (...) {
  .mainNav .li1 .a1 { font-size:.82rem; transition: background .3s ease; }
}
```

### 滚动进场动画 CSS 类匹配检查清单
- JS 使用的类名：`.will-animate`、`.is-visible`
- CSS 对应的选择器必须匹配 JS 使用的类名，不能出现 `.animate-item.will-animate` 而 JS 只加 `will-animate` 的情况
- JS 添加 `.will-animate` 的元素必须与 CSS 选择器范围一致
- 验收：禁用 JS 后元素仍然可见

## 📸 快速验证

```javascript
var el = document.querySelector('.mainNav');
getComputedStyle(el).backgroundColor; // rgb(26, 58, 92)
```

## 🔎 路由优先的模板故障排查

**不要根据用户提到的文件名直接判断线上正在使用的模板。必须先从 URL 反查实际渲染链路。**

### 标准排查顺序

1. 记录可复现 URL、查询参数、语言和设备尺寸。
2. 读取线上最终 HTML，确认页面标题、表单 action、input name、加载的脚本和样式。
3. 根据最终 HTML 的页面结构与脚本路径定位实际模板。
4. 对照 `Views/.../Pages/`、`Views/.../ContentManage/` 和 `Views.Phone/.../Pages/`，确认 CMS 节点绑定的真实文件。
5. 修改本地源文件后，重新上传并清理服务器缓存。
6. 再次读取线上 HTML，确认修改后的标志已经出现；只检查本地文件不能证明线上已生效。

### 搜索页面的实际路由

当前站点的客户端搜索约定如下：

| 语言 | 前台 URL | 实际模板 | 查询参数 | 脚本 | 数据来源 |
|---|---|---|---|---|---|
| 中文 | `/sss` | `Views/jpzougroup/Pages/搜索.cshtml` | `q` | `content/jpzougroup/base/js/search.js` | `window.SEARCH_DATA` |
| 英文 | `/search` | `Views/jpzougroup/Pages/搜索-英文.cshtml` | `q` | `content/jpzougroup/base/js/search.js` | `window.SEARCH_DATA` |
| 手机英文（仅当服务器启用 Phone 模板时） | `/search` | `Views.Phone/jpzougroup/Pages/搜索-英文.cshtml` | `q` | `content.phone/jpzougroup/base/js/search.js` | `window.SEARCH_DATA` |

`Views/jpzougroup/ContentManage/Search/文章搜索结果页*.cshtml` 不是上述 URL 的默认实际模板。除非 CMS 节点明确绑定它，否则只修改该目录不会改变 `/search` 的页面行为。

### 搜索功能必备条件

- 表单使用 `name="q"`，并与 `search.js` 中的 `URLSearchParams(...).get('q')` 保持一致。
- 页面必须加载 `search.js`。
- 页面必须在脚本执行前定义 `window.SEARCH_DATA`。
- 中英文页面使用各自语言的标题、关键词和有效 URL。
- 英文结果链接必须使用已经创建并验证过的英文节点，例如 `/en-sy`、`/en-yjfx`、`/en-yjcg`、`/en-tdcy/en-jsdw`、`/en-tddt/en-tdhd`。
- 修改 `content.phone` 页面时，确认 `content.phone/.../base/js/search.js` 实际存在；不能只引用桌面脚本路径后假设手机资源已同步。

### 搜索故障的快速判定

- 页面显示搜索框但没有结果：先检查是否加载 `search.js` 和是否存在 `window.SEARCH_DATA`。
- 输入有值但脚本读取不到：检查 `name="q"` 与查询参数是否一致。
- 本地有效、线上无结果：检查服务器是否上传了实际绑定的 `Pages/搜索-英文.cshtml`，不要只上传 `ContentManage/Search` 文件。
- 搜索结果链接 404：逐个访问数据数组中的 URL，确认英文节点层级和嵌套标识符。
- 搜索结果只覆盖硬编码页面：这是客户端索引的预期行为；CMS 文章不会自动进入 `SEARCH_DATA`。

### 线上验证命令

```powershell
$html = (Invoke-WebRequest 'https://jpzougroup.nchu.edu.cn/search?q=research' -UseBasicParsing).Content
$html -match 'SEARCH_DATA'
$html -match 'base/js/search.js'
$html -match 'Found'
```

三个检查项至少应能确认页面已加载搜索数据、搜索脚本和结果文案。若线上 HTML 仍显示旧的 `Enter keywords to search site content.`，说明部署或节点绑定没有生效。

## 🧪 “非法内容”模板错误的定位规则

遇到 CMS 报“非法内容”时，不要先批量删除论文、HTML 标签或整段模板。按以下顺序做单变量定位：

1. 与可以正常保存的模板比较编码、BOM、换行、文件大小、Razor 指令和标签闭合。
2. 扫描新增文本中只出现一次的敏感词，尤其是英文科研标题里的独立词。
3. 将一个可疑词临时替换为普通词，只测试一个变量。
4. 记录是保存、上传、编译还是页面渲染阶段报错。
5. 在确认触发词后，再决定使用 HTML 实体、拆分文本或 CMS 数据驱动；不要把临时替换词当作正式论文内容。

本项目实际出现过的高风险候选包括论文标题中的独立词 `system`，以及期刊文本中的 `Environment`。这类词可能被模板安全扫描器误判为 `System` / `Environment` API 访问；此结论必须通过单变量测试确认，不能直接当作服务器规则。

## 📱 手机版实测规则

使用 390 × 844 视口至少检查一次：

- 语言切换链接的可见区域不能是 0 × 0；
- 轮播左右按钮不能覆盖标题和 CTA；
- 首屏内容不能因为 `opacity:0` 永久隐藏；
- 头部导航不得产生横向滚动；
- 论文、新闻和活动卡片应能单列排列；
- 线上实际加载的 CSS/JS 路径必须与选定的响应式架构一致。

浏览器审计结论必须区分“响应式桌面模板”与“服务器 UA 命中的 `Views.Phone` 模板”，不能仅凭本地存在 `Views.Phone` 目录就断定手机站已经启用。