# 此木的车间 · 双站点项目总览

> **本文件作用**：覆盖 hvac-lab（知识库）+ cimugarage-blog（博客）两个站点的完整状态、经验教训、当前进度。下一次对话直接阅读本文件即可无缝开始任务。

---

## 一、项目身份

| 维度 | hvac-lab（知识库） | cimugarage-blog（博客） |
|------|-------------------|------------------------|
| 域名 | hvac.cimugarage.cn | cimugarage.cn（当前走 ESA 测试子域名） |
| 仓库 | [kingfps007/hvac-lab](https://github.com/kingfps007/hvac-lab) | [kingfps007/cimugarage-blog](https://github.com/kingfps007/cimugarage-blog) |
| 本地路径 | `c:\Users\King\Desktop\此木暖通车间\` | `c:\Users\King\Desktop\此木暖通车间\blog-scaffold\` |
| 性质 | 暖通专业知识库 + 工程工具 | 个人博客（专题文章、技术折腾、生活记录） |
| 主题模式 | `site_type: kb` | `site_type: blog`（通过 `_config.cimu-kb.yml` 覆盖） |
| 语言 | 中文 | 中文（默认）+ 英文 |
| 部署 | GitHub → 阿里云 ESA Pages | GitHub → 阿里云 ESA Pages |
| 可见性 | public | public |

### 架构关键决策（2026-07-03）

**双仓库独立架构**：hvac-lab 和 cimugarage-blog 各自**内联** cimu-kb 主题代码，不再使用 git submodule 同步。

- ✅ hvac-lab 主题代码固化在 `themes/cimu-kb/`（kb 模式专用，已清理 blog 残留）
- ✅ cimugarage-blog 主题代码固化在 `themes/cimu-kb/`（blog 模式专用，含 blog-sidebar.ejs 等）
- ✅ 不再需要 3 仓库同步
- ✅ GitHub 上的 cimu-kb 仓库已不再被引用（保留作为历史备份，可手动删除）
- ✅ 本地备份在 `c:\Users\King\Desktop\cimu-kb-backup-20260703`（kb 版）和 `cimu-kb-blog-backup-20260703`（blog 版）

**未来工作流**：
- 改博客主题 → 只 push `cimugarage-blog`
- 改知识库主题 → 只 push `hvac-lab`
- 互不干扰

---

## 二、当前进度（2026-07-03）

### 2.1 ✅ 已完成（P0 主体）

#### hvac-lab（知识库）
- ✅ 12 篇暖通知识库文章（建筑自控 7 篇 + 冷热源 5 篇 + 暖通空调 1 篇）
- ✅ 水力计算工具页（`/hvac/tools/hydraulic-calc.html`）
- ✅ cimu-kb 主题（kb 模式三栏布局：侧栏 + 正文 + 目录 + 进度条）
- ✅ 报纸配色 + 暗色模式（不跟随系统，localStorage 持久化）
- ✅ 站内搜索（hexo-generator-searchdb）
- ✅ KaTeX 数学公式（完全本地化，无 CDN）
- ✅ ESA Pages 部署
- ✅ 备案号 + 公安备案
- ✅ 主题代码内联化（删除 submodule 引用）

#### cimugarage-blog（博客）
- ✅ 7 篇文章迁移（5 中文 + 2 双语，从 Halo 后台确认）
- ✅ cimu-kb 主题（blog 模式：报纸感首页 + 知识库风格文章页）
- ✅ 报纸配色（`--paper-masthead` 米黄 / `--ink-headline` 焦糖）
- ✅ 折叠侧栏（`<details>` 6 分区：分类/标签/最新/归档/关于+版权）
- ✅ 移动端汉堡抽屉（mobile-nav-panel）
- ✅ 双语切换（单按钮，检测英文版是否存在，不存在回退到 `/en/`）
- ✅ 暗色模式（不跟随系统，localStorage 持久化）
- ✅ 站内搜索（Fuse.js 本地）
- ✅ 代码复制按钮
- ✅ PJAX（无刷新加载）
- ✅ 阅读进度条
- ✅ 404 错误页
- ✅ 隐私政策页面（中英双语）
- ✅ ESA Pages 部署
- ✅ 本地化 CDN（无 jsdelivr/cdnjs/Google Fonts/Gravatar）
- ✅ 主题代码内联化（删除 submodule 引用）
- ✅ 文章目录（`.blog-toc` 独立样式，焦糖色背景区分 kb 模式）
- ✅ Halo 旧 URL 重定向表（`_redirects` 文件）
- ✅ URL slug 简化（超长拼音 slug → 英文短 slug）
- ✅ 非文章页面移到 `source/` 根目录（避免被归档页收录）

### 2.2 🔄 P0 收尾（立即可做）

1. **图片迁移**：从 Halo 备份复制 16 张图到 `source/assets/images/<slug>/`，当前保留原格式，后续转 WebP
2. **Moments 归档页**：创建 `source/moments/{index,en}.md` + `source/_data/moments.yml` + `layout/moments.ejs`
3. **404 错误页样式打磨**（与报纸配色统一）
4. **切主域 cimugarage.cn**（依赖用户关闭 Halo 容器 + 域名解析切换）
5. **删除 GitHub cimu-kb 仓库**（需用户手动在 Web 界面操作）

### 2.3 ⏳ P1 第一波（前端独立完成）

6. **双语一致性检查脚本** `tools/check-bilingual.js`
7. **AI 候选封面图**（仅首页 lead story 用）

### 2.4 ⏳ P1 第二波（ESA 函数，需配置控制台）

8. **评论 API**：`/api/comment` + `/api/comments` + 后台 `/admin/comments`
9. **浏览数 API**：`/api/view` + `/api/view/count`
10. **浏览排行榜 API**：`/api/top-posts`
11. **KV 备份 GitHub Actions**（每日导出 `backups/kv-YYYYMMDD.json`）

### 2.5 ⏳ P2 长期

12. Trilium 模板适配
13. Dependabot + CI 工作流
14. UptimeRobot 监控接入
15. Sitemap hreflang 后处理脚本
16. 维护页 503 实测

---

## 三、关键配置

### 3.1 hvac-lab `_config.yml` 关键字段

```yaml
title: 此木暖通车间
subtitle: CIMU HVAC LAB
author: 此木的车间
url: https://hvac.cimugarage.cn
language: zh-CN
timezone: Asia/Shanghai
theme: cimu-kb
# site_type 默认 kb（在 themes/cimu-kb/_config.yml 中）
```

### 3.2 cimugarage-blog `_config.yml` 关键字段

```yaml
title: 此木的车间
title_en: "This Wood's Workshop"
subtitle: 个人博客 · 专题文章 · 技术折腾 · 生活记录
subtitle_en: "Personal Blog · Tech Notes · Life Records"
author: 此木的车间
url: https://cimugarage.cn
language: zh-CN
timezone: Asia/Shanghai
icp: '晋ICP备2026000871号-1'
police: '晋公网安备14010902001829号'
since: 2026
theme: cimu-kb
permalink: :title/
```

### 3.3 cimugarage-blog `_config.cimu-kb.yml`（主题覆盖配置）

**关键**：Hexo 7 通过 `_config.<theme_name>.yml` 文件覆盖主题 `_config.yml`。blog 模式通过这个文件启用：

```yaml
site_type: blog
blog:
  sidebar:
    show_categories: true
    show_tags: true
    show_recent: true
    recent_count: 5
    tag_cloud_count: 20
  home:
    headline_count: 1
    excerpt_length: 120
    show_dates: true
    show_tags: true
  post:
    show_toc: true
    show_article_nav: true
    article_list_mode: 'date'
```

### 3.4 ESA Pages 部署配置

| 字段 | hvac-lab | cimugarage-blog |
|------|---------|-----------------|
| 安装命令 | `npm ci` | `npm ci` |
| 构建命令 | `npm run build` | `npm run build && cp _redirects public/_redirects`（postbuild 脚本） |
| 静态资源目录 | `public` | `public` |
| Node.js 版本 | 20.20.2 | 20.20.2（package.json engines 锁定） |

### 3.5 Git 配置

```
Git author: 此木的车间 <2479010668@qq.com>
提交命令示例:
git -c user.name="此木的车间" -c user.email="2479010668@qq.com" commit -m "..."
```

---

## 四、文章内容结构

### 4.1 hvac-lab 知识库文章

```
source/kb/
├── overview.md                    # 建筑自控总览
├── heating-cooling-overview.md    # 冷热源总览
├── sensors.md                     # 暖通传感器
├── actuators.md                   # 暖通常用执行器
├── control-loop.md                # 控制回路
├── algorithms.md                  # 控制算法
├── modbus.md                      # MODBUS 通信协议
├── refrigeration-cycle.md         # 制冷循环基础
├── refrigerants.md                # 制冷剂与工质
├── refrigeration-equipment.md    # 制冷设备
├── refrigeration-systems.md      # 制冷与热泵系统
├── boilers.md                     # 锅炉设备
└── hvac/                          # 暖通空调知识库
    ├── hvac-intro.md
    ├── hvac-load.md
    ├── hvac-heating.md
    ├── hvac-ac.md
    ├── hvac-air-distribution.md
    └── hvac-smoke-control.md
```

### 4.2 cimugarage-blog 文章

```
source/_posts/
├── <slug>/
│   ├── index.md      # 中文（默认，lang=zh-CN）
│   └── en.md         # 英文（lang=en，可选）
```

**当前 7 篇文章**：

| slug | 标题 | 双语 | 日期 |
|------|------|------|------|
| security-of-blog | 博客安全 | ✅ | 2026-01-21 |
| wai-she-equipments | 外设记录 | ✅ | 2026-03-06 |
| linux-intro | linux 入门问题排查记录 | ❌ | 2026-03-07 |
| fnos-to-linuxnas | 飞牛转 LinuxNas | ❌ | 2026-03-08 |
| chai-chu-raid-he-lvm | 拆除 RAID 和 LVM | ❌ | 2026-03-08 |
| ji-yu-chao-sheng-bo-... | 基于超声波雾化...蒸发冷却净化加湿系统 | ❌ | 2026-05-14 |

### 4.3 双语 front matter 规范

```markdown
---
title: 文章标题
date: 2026-01-21 14:39:00
updated: 2026-02-19 07:57:00
lang: zh-CN              # 中文版 / en 英文版
category: tech-learned
tags: [security, blog]
license: CC BY 4.0
description: 摘要
---
```

**强制要求**：
- `date` / `updated` / `lang` 必须存在
- `category` / `tags` 双语版必须完全一致
- `permalink` 字段用于强制路由（英文版 `permalink: en/<slug>/`）

### 4.4 非文章页面（放 source/ 根目录，不放 _posts/）

```
source/
├── index.md          # 首页
├── about/            # 关于页
├── 404/              # 404 错误页
├── en-home/          # 英文首页
├── moments/          # 瞬间归档页（待创建）
└── _posts/           # 仅文章
```

> **陷阱**：非文章页面放 `_posts/` 会被 Hexo 当文章收录到归档页，需放 `source/` 根目录。

---

## 五、经验教训（踩过的坑）

### 5.1 Hexo 配置陷阱

**陷阱 1：YAML 整数类型**
- `type: 404` 被 YAML 解析为整数，EJS 中需用 `==` 宽松比较
- **解决**：用引号 `type: "404"` 或 EJS 中 `page.type == 404`

**陷阱 2：`page.path` 不带前导 `/`**
```javascript
page.path === 'security-of-blog/'        // 中文版
page.path === 'en/security-of-blog/'     // 英文版（带 /en/ 前缀）
```

**陷阱 3：i18n 切换时去掉/添加 `/en/` 前缀**
```javascript
// 中文 → 英文：检测英文版是否存在
const enPath = (page.path || '').replace(/^([^/]+)/, 'en/$1');
const enExists = site.posts.some(p => p.path === enPath || p.path === enPath.replace(/\/$/, '') + '/');
langSwitchUrl = enExists ? '/en/' + (page.path || '') : '/en/';

// 英文 → 中文：中文版一定存在
langSwitchUrl = '/' + (page.path || '').replace(/^en\//, '');
```

**陷阱 4：`_posts/` 目录会收录非文章**
- 非文章页面（about/404/en-home/index）放在 `source/_posts/` 会被当文章收录到归档
- **解决**：移到 `source/` 根目录

**陷阱 5：Hexo 7 主题覆盖配置机制**
- 主题 `_config.yml` 默认 `site_type: kb`
- blog 模式通过站点根目录 `_config.cimu-kb.yml` 文件覆盖（Hexo 7 原生机制）
- 不是通过主 `_config.yml` 的 `theme_config` 字段

### 5.2 主题架构陷阱

**陷阱 1：kb/blog 模式混用 footer**
- footer.ejs 被 layout.ejs 无条件渲染（kb + blog 都渲染）
- **解决**：footer.ejs 保持 kb 原貌（仅版权+备案号），blog 模式 footer 在 blog-sidebar.ejs 的 `.blog-sidebar-footer` 里

**陷阱 2：toc.js 选择器不兼容 blog 模式**
- 原 `document.querySelector('.kb-main article')` 在 blog 模式下找不到元素
- **解决**：改为 `document.querySelector('.kb-main article, .blog-main article')`

**陷阱 3：toggleToc 函数未定义**
- layout.ejs 中按钮 onclick="toggleToc()" 引用但函数从未定义
- **解决**：在 layout.ejs 的 `<script>` 块里补全 toggleToc 函数，同时操作 kbToc + blogToc

**陷阱 4：blog 模式 toc 误用 kb-toc 样式**
- layout.ejs 中 blog 模式 toc 容器误用 `class="kb-toc" id="kbToc"`
- **解决**：改为 `class="blog-toc" id="blogToc"`，独立 CSS 样式（焦糖色背景区分）

### 5.3 Git 与部署陷阱

**陷阱 1：ESA 测试 URL 每次构建失效**
- 每次构建生成新的 `esa_er_token`，旧 token 自动失效（401）
- **解决**：用户需从 ESA 控制台获取最新测试 URL

**陷阱 2：PowerShell here-string 不支持嵌套特殊字符**
- `@'...'@` 内含 `<%` 等 EJS 标签会出错
- **解决**：用 Write 工具直接写文件，不用 PowerShell here-string

**陷阱 3：submodule 同步 3 仓库成本高**
- 每次改博客主题都要同步 cimu-kb → blog-scaffold → hvac-lab
- **解决**：双仓库独立架构，内联化主题代码（已完成）

**陷阱 4：commit message BOM 字符**
- PowerShell 写入的 commit message 可能有 UTF-8 BOM 前缀
- **解决**：用 `[System.IO.Path]::GetTempFileName()` + `[System.IO.File]::WriteAllText()` 写临时文件

### 5.4 内容与样式陷阱

**陷阱 1：超长拼音 slug**
- Halo 旧文章 slug 是超长拼音（`ji-yu-chao-sheng-bo-wu-hua-yu-...`）
- **解决**：简化为英文短 slug（`ultrasonic-humidifier`）+ `_redirects` 301 重定向

**陷阱 2：`© 2026–2026` 年份范围**
- `since: 2026` 和当前年份都是 2026，显示 `© 2026–2026`
- **解决**：`const yearRange = sinceYear === currentYear ? String(currentYear) : (sinceYear + '–' + currentYear)`

**陷阱 3：归档页宽度不一致**
- 分类/标签页 `.archive-wrap` 用了 `max-width: 880px`，比首页 `newspaper-wrap` 1280px 窄
- **解决**：`.archive-wrap` 改为 `max-width: 1280px`

**陷阱 4：文章背景不区分**
- 文章卡片无背景/阴影/边框，视觉上连成一片
- **解决**：`.blog-main article` 加 `background: var(--paper-main)` + `box-shadow` + `border`

**陷阱 5：全宽 footer 不需要**
- blog 模式不需要全宽 footer，只在侧栏底部有即可
- **解决**：layout.ejs 中移除 blog 模式的全宽 footer 渲染

### 5.5 KaTeX 公式陷阱（hvac-lab）

- KaTeX 不支持 CJK 字符（无论 `\text{}` 还是数学模式）
- **解决**：含中文的公式改用 HTML/CSS `.formula-box` + `.formula-frac` 渲染
- `$$...$$` 必须单行（marked 多行会插入 `<br>` 切断配对）
- 表格内半角 `~` 改全角 `～`（避免 marked GFM 解析为 `<del>`）

---

## 六、关键命令

### 6.1 本地构建验证

```powershell
# hvac-lab（知识库，kb 模式）
cd "c:\Users\King\Desktop\此木暖通车间"
Remove-Item -Recurse -Force public, db.json -ErrorAction SilentlyContinue
npx hexo generate    # 应生成 41 files

# cimugarage-blog（博客，blog 模式）
cd "c:\Users\King\Desktop\此木暖通车间\blog-scaffold"
Remove-Item -Recurse -Force public, db.json -ErrorAction SilentlyContinue
npx hexo generate    # 应生成 94 files
```

### 6.2 本地预览

```powershell
npx hexo server      # http://localhost:4000
```

### 6.3 提交推送

```powershell
# cimugarage-blog
cd blog-scaffold
git add -A
git -c user.name="此木的车间" -c user.email="2479010668@qq.com" commit -m "fix: ..."
git push origin main

# hvac-lab
cd ..
git add -A
git -c user.name="此木的车间" -c user.email="2479010668@qq.com" commit -m "fix: ..."
git push origin main
```

### 6.4 WebP 转换（需 cwebp 在 PATH）

```powershell
# 单张
cwebp -q 80 image.png -o image.webp

# 批量
Get-ChildItem source\assets\images\<post>\*.png | ForEach-Object {
  cwebp -q 80 $_.FullName "$($_.DirectoryName)\$($_.BaseName).webp"
}
```

---

## 七、URL 与路由

### 7.1 cimugarage-blog URL 模式

| 类型 | 模式 | 示例 |
|------|------|------|
| 中文首页 | `/` | `cimugarage.cn/` |
| 英文首页 | `/en/` | `cimugarage.cn/en/` |
| 中文文章 | `/<slug>/` | `cimugarage.cn/security-of-blog/` |
| 英文文章 | `/en/<slug>/` | `cimugarage.cn/en/security-of-blog/` |
| 归档 | `/archives/` | `cimugarage.cn/archives/` |
| 分类 | `/categories/<cat>/` | `cimugarage.cn/categories/tech-learned/` |
| 标签 | `/tags/<tag>/` | `cimugarage.cn/tags/security/` |
| 关于 | `/about/` | `cimugarage.cn/about/` |
| 隐私政策 | `/about/privacy/` | `cimugarage.cn/about/privacy/` |

### 7.2 旧 Halo URL 重定向

`_redirects` 文件（cimugarage-blog 根目录），构建时复制到 `public/_redirects`：

```
/archives/<old-slug>/  /<new-slug>/  301
```

每次新增文章同步检查旧 URL。

---

## 八、主题文件位置

### 8.1 共用文件（kb + blog 模式都用）

```
themes/cimu-kb/
├── _config.yml                    # 主题配置（默认 site_type: kb）
├── layout/
│   ├── layout.ejs                 # 全局骨架（含 site_type 判断分支）
│   ├── index.ejs                   # 首页模板
│   └── _partial/
│       ├── header.ejs             # 导航栏（含移动端 navbar + closeAllPanels）
│       ├── footer.ejs             # kb 模式页脚（仅版权+备案号）
│       ├── sidebar.ejs            # kb 模式侧栏树
│       ├── breadcrumb.ejs         # 面包屑
│       └── search-overlay.ejs     # 搜索面板
├── source/
│   ├── css/style.css              # 全局样式（含 .kb-toc + .blog-toc）
│   ├── js/
│   │   ├── nav-tree.js            # kb 模式侧栏树
│   │   ├── toc.js                 # 文章目录（双模式：.kb-main article, .blog-main article）
│   │   └── search.js              # 本地搜索
│   ├── lib/katex/                 # KaTeX 本地化
│   └── fonts/                     # 本地化字体
```

### 8.2 blog 模式专用文件（仅 cimugarage-blog 有）

```
themes/cimu-kb/layout/_partial/
├── blog-sidebar.ejs               # blog 模式侧栏（6 分区 + footer）
└── (其他 blog 专用 partial)
```

### 8.3 layout.ejs 渲染分支

```
<% if (page.type === 'kb' || (theme.site_type === 'kb' && page.layout === 'post')) { %>
  ... kb 模式（三栏：sidebar + main + toc）
<% } else if (theme.site_type === 'blog' && page.layout === 'post') { %>
  ... blog 模式文章页（newspaper-layout + sidebar + blog-toc）
<% } else if (page.posts) { %>
  ... 归档/分类/标签页（newspaper-layout + sidebar）
<% } else { %>
  ... 通用页（about/privacy/404 等）
<% } %>
```

---

## 九、侧栏结构（blog 模式）

`blog-sidebar.ejs` 分区顺序：

1. **品牌区**：站名（可点回首页）+ 副标 + 元信息（日期+文章数）
2. **主导航**：归档 / 瞬间
3. **功能按钮区**：主题切换 / 语言切换 / 搜索 / 瞬间
4. **分类**（`<details>` 默认展开）
5. **标签**（`<details>` 默认折叠）
6. **最新**（`<details>` 默认展开）
7. **归档按钮**（不是树，只是链接到 /archives/）
8. **页脚**：关于 / 隐私政策 / 版权 / CC BY 4.0 / 备案号 + 公安备案

---

## 十、隐私与合规

### 10.1 收集什么

| 数据 | 必要性 | 是否公开 |
|------|--------|---------|
| 昵称 | 必填 | 公开 |
| 评论内容 | 必填 | 公开 |
| 邮箱 | 选填 | 不公开（仅 hash 存） |
| 网站 | 选填 | 公开 |
| IP | 自动 | 不公开（仅 hash 存） |

### 10.2 不收集什么

- ❌ 真实姓名 / ❌ 手机号 / ❌ 地理位置 / ❌ 浏览器指纹 / ❌ 跨站追踪 ID

### 10.3 备案

- 晋ICP备2026000871号-1
- 晋公网安备14010902001829号
- 两处显示：侧栏底部 + 移动端面板底部

---

## 十一、规则文件索引

| 文件 | 位置 | 作用 |
|------|------|------|
| `blog_workflow.md` | `.trae/rules/` | 博客站工作流约定（v0.8） |
| `comment_workflow.md` | `.trae/rules/` | 评论系统实现规范 |
| `media_workflow.md` | `.trae/rules/` | 媒体资源规范 |
| `extensibility.md` | `.trae/rules/` | 扩展性架构（组件注册/资源加载） |
| `project_rules.md` | `.trae/rules/` | 通用项目规则 |
| `BLOG_MIGRATION_DESIGN.md` | 项目根 | 迁移设计文档 v0.8（上游） |
| `SETUP_GUIDE.md` | 项目根 | 开发搭建指南 |

---

## 十二、下一次对话需要知道的事

### 12.1 立即可做的任务

1. **图片迁移**：从 `.halo-backup-tmp/` 复制 16 张图到 `blog-scaffold/source/assets/images/<slug>/`
2. **Moments 归档页**：创建 `source/moments/{index,en}.md` + 数据文件 + 模板
3. **删除 GitHub cimu-kb 仓库**：用户手动在 [GitHub 设置](https://github.com/kingfps007/cimu-kb/settings) → Danger Zone → Delete

### 12.2 修改主题代码时

- **改博客主题** → 只改 `blog-scaffold/themes/cimu-kb/` → push `cimugarage-blog`
- **改知识库主题** → 只改 `themes/cimu-kb/`（hvac-lab 根目录）→ push `hvac-lab`
- **不要** 试图同步两个仓库的主题代码

### 12.3 验证修改的命令

```powershell
# 构建验证
cd <仓库目录>
Remove-Item -Recurse -Force public, db.json -ErrorAction SilentlyContinue
npx hexo generate

# 验证 HTML 产物
Select-String -Path 'public\index.html' -Pattern '要检查的字符串'
```

### 12.4 常见问题排查

| 问题 | 排查方向 |
|------|---------|
| 页脚出现归档/隐私政策（kb 站） | footer.ejs 是否被改回 blog 风格 |
| 文章目录为空 | toc.js 选择器是否包含 `.blog-main article` |
| 语言切换 404 | 英文版是否存在，不存在应回退到 `/en/` |
| 归档页收录假文章 | 非文章页面是否在 `_posts/` 下（应移到 `source/`） |
| `© 2026–2026` | yearRange 逻辑是否正确（同年只显示单年份） |
| 分类/标签页宽度不一致 | `.archive-wrap` max-width 是否为 1280px |
| 构建失败 | `npm ci` 重装依赖 / 检查 front matter YAML 语法 |

---

## 十三、Git 历史要点

### 13.1 最新 commits

**hvac-lab**：
```
92bb13f chore: 内联 cimu-kb 主题代码（删除 submodule 引用）+ 清理 blog 残留
10b065a chore: 同步 cimu-kb 子模块至 fec2fd8
8fe938a feat: hvac.cimugarage.cn 暖通知识库
```

**cimugarage-blog**：
```
79b91a6 fix: blog 模式目录独立样式 + toggleToc 函数补全
b699708 chore: 内联 cimu-kb 主题代码（删除 submodule 引用）
892dca6 fix: Q1-Q8 修复 + 文件结构整理
```

### 13.2 仓库可见性

- hvac-lab: public
- cimugarage-blog: public
- cimu-kb: public（已不再被引用，可删除）

---

## 十四、hvac-lab 历史更新记录

> 以下是 hvac-lab（知识库）的历史更新摘要。完整历史见 git log。

**v4.5（2026-07-01）**：依赖清理 + assets/ 目录骨架 + components/ 组件骨架
**v4.4.10（2026-06-05）**：二次回风教材公式链完整推导
**v4.4.9（2026-06-05）**：二次回风计算流程详细补充
**v4.4.8（2026-06-05）**：机械 vs 重力循环偏导数对比分析 + 水力计算工具 UX 改进
**v4.4.7（2026-06-05）**：KaTeX CJK 公式彻底修复（改用 HTML/CSS）
**v4.4.6（2026-06-05）**：页面默认浅色（不跟随系统）
**v4.4.5（2026-06-05）**：`\text{中文}` 红色明文修复（CSS 字体 fallback）
**v4.4.4（2026-06-05）**：12 个公式补结尾 `$$` + 深色模式防白闪
**v4.4.3（2026-06-05）**：KaTeX 完全本地化（删除 jsdelivr CDN）
**v4.4（2026-06-05）**：新增「暖通空调」知识库
**v4.3（2026-06-05）**：全文零公共 CDN
**v4.0（2026-06-04）**：仓库完全重置 + .trae/rules/ 规则体系建立
