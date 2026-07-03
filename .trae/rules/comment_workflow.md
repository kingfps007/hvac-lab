# 评论系统工作流

> **本文件目标**：把评论系统的实现方案、扩展方式、隐私考量沉淀到规则里。

---

## 〇、为什么需要规则

评论是**有状态、需要后端、涉及用户数据**的功能，最容易出问题和留下技术债。规则化是为了：
- 避免重复选型讨论
- 统一评论数据格式
- 沉淀隐私与反垃圾经验

---

## 一、当前推荐方案：ESA Pages 函数 + KV 存储

### 1.1 架构

```
用户浏览器
   ↓ POST /api/comment
阿里云 ESA Pages 函数（Node.js）
   ↓ 写入
阿里云 ESA KV（键值数据库）
   ↓ 读取
阿里云 ESA Pages 函数 GET /api/comments
   ↓ 返回
用户浏览器渲染评论
```

### 1.2 数据模型

KV 中每条评论的 key：
```
comment:{文章路径}  →  JSON 数组
```

数据结构：
```json
[
  {
    "id": "uuid-v4",
    "article": "kb/sensors/index",
    "author": {
      "name": "访客昵称",
      "email": "可选（不公开）",
      "website": "可选",
      "avatar": "gravatar URL（基于 email）"
    },
    "content": "评论内容（Markdown 或纯文本）",
    "createdAt": "2026-06-04T10:30:00Z",
    "ip_hash": "sha256(ip + salt)",
    "status": "approved"  // approved | pending | spam
  }
]
```

### 1.3 ESA 函数代码位置

```
functions/                            # ★ ESA 函数根目录
├── comment/
│   ├── package.json
│   ├── index.js                      # GET /api/comments, POST /api/comment
│   └── README.md
└── _shared/
    └── utils.js                      # 通用工具（IP hash、敏感词等）
```

**注意**：ESA 配置中需把**函数文件路径**指向 `functions/`，**静态资源目录**仍是 `public`。

⚠️ **当前项目是纯静态站**，**函数文件路径留空**。如果将来要加评论：
1. 在 ESA 控制台把函数文件路径改为 `functions`
2. 把 `functions/` 目录加入仓库
3. 同步更新 [project_rules.md](file:///c:/Users/King/Desktop/260528hvac-hexo-trea/.trae/rules/project_rules.md) 的部署配置章节

---

## 二、反垃圾与隐私

### 2.1 必须做的事

- [ ] **IP hash 存储**：原始 IP 不入库，只存 `sha256(ip + salt)`
- [ ] **频率限制**：同一 IP 5 分钟内最多 1 条评论
- [ ] **长度限制**：评论 1-2000 字符
- [ ] **XSS 防护**：前端用 DOMPurify 清理
- [ ] **敏感词过滤**：基础敏感词列表
- [ ] **人工审核**：所有评论先入 `pending` 状态
- [ ] **删除权**：提供 DELETE 接口（带 admin token）

### 2.2 隐私合规

- [ ] **隐私政策页面**：写明收集什么、不收集什么
- [ ] **可选字段**：email 和 website 设为可选
- [ ] **导出/删除**：提供"我的评论"功能（基于 IP hash 识别）
- [ ] **数据保留**：定期清理 1 年前的评论

### 2.3 反垃圾技术

- **蜜罐字段**：表单隐藏字段，机器人会填，人类不会
- **reCAPTCHA** 或 **hCaptcha**：v3 版（无感）
- **Akismet**：经典方案
- **自建敏感词**：基于 [naivebayes](https://github.com/biggora/naivebayes) 或简单正则

---

## 三、前端组件规范

### 3.1 组件位置

```
themes/cimu-kb/source/js/components/comment/
├── index.js
├── styles.css
└── README.md
```

### 3.2 数据接口

```javascript
// 组件初始化
init(container, {
  articlePath: 'kb/sensors/index',  // 当前文章路径
  apiBase: '/api',                  // ESA 函数前缀
  pageSize: 20,                     // 每页评论数
  showAvatar: true,                 // 是否显示头像
  allowReply: false,                // 是否允许回复（v2）
})
```

### 3.3 渲染逻辑

```javascript
// 列表渲染
async function loadComments() {
  const res = await fetch(`${apiBase}/comments?article=${articlePath}`);
  const data = await res.json();
  return data.comments;  // 数组
}

// 提交评论
async function submitComment(content, author) {
  const res = await fetch(`${apiBase}/comment`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      article: articlePath,
      author,
      content,
    }),
  });
  return res.json();
}
```

### 3.4 Markdown 中使用

```markdown
[component:comment article="kb/sensors/index"]
```

或在 Front Matter 中：
```yaml
---
title: 文章标题
comment:
  enabled: true
  showAvatar: true
---
```

---

## 四、扩展场景

### 4.1 嵌套回复

v2 版本支持回复：
- 增加 `parentId` 字段
- 树形渲染（最多 2 级）
- @ 提及通知（可选）

### 4.2 用户系统

v3 版本支持登录：
- GitHub OAuth（推荐）
- 邮箱验证码
- 登录后显示"已登录"标识
- 个人评论历史

### 4.3 实时通知

v4 版本支持实时：
- WebSocket 或 Server-Sent Events
- 有人回复你时推送
- 邮件通知（可选）

### 4.4 反垃圾升级

- 接入 AI 审核（ESA 函数调用通义千问）
- 行为分析（点击/滚动/停留时间）
- 黑白名单系统

---

## 五、评论迁移方案

如果将来要从一个评论系统迁移到另一个：

1. **导出脚本**：写一个 `migrate.js` 从旧系统拉数据
2. **格式转换**：转成统一 JSON 格式
3. **导入新系统**：批量写入 KV
4. **URL 映射**：保持旧评论的 ID 可访问（可选）

**建议**：从一开始就选定方案，**不要频繁迁移**。

---

## 六、评论与隐私的边界

### 6.1 收集什么

| 数据 | 必要性 | 是否公开 |
|------|--------|---------|
| 昵称 | 必填 | 公开 |
| 评论内容 | 必填 | 公开 |
| 邮箱 | 选填 | 不公开（仅用于 Gravatar） |
| 网站 | 选填 | 公开 |
| IP | 自动 | 不公开（仅 hash 存） |
| User-Agent | 自动 | 不公开（仅用于反垃圾） |

### 6.2 不收集什么

- ❌ 真实姓名
- ❌ 手机号
- ❌ 地理位置
- ❌ 浏览器指纹
- ❌ 跨站追踪 ID

### 6.3 用户权利

- [ ] **查看**：能看到自己所有评论
- [ ] **修改**：7 天内可修改
- [ ] **删除**：随时可删除（带 IP hash 验证）
- [ ] **导出**：可导出 JSON 格式

---

## 七、检查清单

**实现评论功能前**：

- [ ] 已在 ESA 控制台配置函数路径
- [ ] 已有 `functions/` 目录
- [ ] 已有 KV 命名空间
- [ ] 隐私政策已更新
- [ ] 反垃圾策略已设计
- [ ] 前端组件已就绪（基于 `comment` 组件模板）
- [ ] 测试评论、回复、删除流程
- [ ] 在 README 中更新"评论"功能说明

**完成后**：

- [ ] 监控评论 API 调用量
- [ ] 定期审查 spam
- [ ] 清理过期评论（>1 年）
- [ ] 备份评论数据

---

## 八、相关文件

| 文件 | 作用 |
|------|------|
| `functions/comment/index.js` | ESA 函数入口 |
| `themes/cimu-kb/source/js/components/comment/index.js` | 前端组件 |
| `themes/cimu-kb/source/js/components/registry.js` | 注册表（已含 comment） |
| `source/about/privacy.md`（待创建） | 隐私政策 |
| `source/_data/comment-config.yml`（待创建） | 评论全局配置 |
