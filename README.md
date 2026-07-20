# ZJP Group Site Skills

邹建平课题组网站开发与维护的可复用技能集合。

## Skills

### 1. powereasy-template-adapter
PowerEasy CMS 模板适配指南。涵盖：
- Razor 模板开发规则
- CSS 覆写模式（层叠覆盖 CMS 原生 HTML）
- 双语实现（中英文切换策略）
- 手机端响应式改造（汉堡菜单、抽屉导航、轮播触摸、滚动动画）
- 响应式断点策略
- "非法内容"错误定位规则
- 搜索功能路由排查

### 2. zup-group-site-manager
ZJP 课题组网站完整管理手册。涵盖：
- 本地目录结构与文件映射
- Pages 清单（24 个模板文件）
- 模板开发工作流
- 中英文部署策略
- Logo 不加载修复
- 论文卡片加跳转链接
- 首页结构调整
- 搜索实现方案（客户端 SEARCH_DATA）
- CSS 常见陷阱
- 线上模板验证流程

## 使用方式

将这些 skills 放入 OpenCode/Claude 的 skills 目录即可使用：

```
# Windows
XCOPY /E /I zjp-group-site-skills\* %USERPROFILE%\.cc-switch\skills\
```

## 仓库维护

- 主分支：`main`
- 内容：SKILL.md 及其依赖的脚本、参考文件
- 更新节奏：每次网站开发迭代后同步更新
