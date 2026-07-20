# API数据导入指南

## 概述
通过Token+管理员ID认证，调用动易CMS API批量导入数据（新闻、论文、成员等）。

## 认证信息
- Token: 在后台"用户与权限管理"中创建/重置
- Admin ID: `jpzougroup`
- 认证头: `Authorization: Bearer {token}`
- 附加头: `AdminId: jpzougroup`

## API端点（常见模式）

动易CMS的API通常遵循以下模式：

| 操作 | 方法 | 路径 |
|------|------|------|
| 获取文章列表 | GET | `/api/ContentManage/Article/GetList` |
| 新增文章 | POST | `/api/ContentManage/Article/Add` |
| 更新文章 | POST | `/api/ContentManage/Article/Update` |
| 删除文章 | POST | `/api/ContentManage/Article/Delete` |

**注意：** 具体API路径以后台"帮助文档"中列出的为准。API可能仅限校内网访问。

## 请求格式

```json
POST /api/ContentManage/Article/Add
Authorization: Bearer {token}
AdminId: jpzougroup
Content-Type: application/json

{
  "title": "文章标题",
  "content": "<p>文章正文HTML内容</p>",
  "nodeIdentifier": "栏目标识符",
  "keywords": "关键词1,关键词2",
  "author": "作者",
  "source": "来源",
  "publishTime": "2026-07-14 10:30:00",
  "description": "文章摘要",
  "status": "Published"
}
```

## 响应格式

```json
{
  "status": 1,
  "message": "操作成功",
  "data": {}
}
```

## 安全性
- Token应严格保密
- 使用后建议在后台重置Token
- 不要在代码仓库中硬编码Token
