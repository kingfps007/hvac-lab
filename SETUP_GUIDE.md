# 此木暖通车间 · Hexo 搭建指南

## 📋 项目结构

```
hvac-hexo/
├── _config.yml                    # Hexo 主配置
├── package.json                   # 依赖配置
├── README.md                      # 项目说明与更新记录
├── .editorconfig                  # 编码规范配置
├── .gitignore                     # Git 忽略文件
├── source/
│   ├── index.md                   # 首页
│   ├── about/index.md             # 关于页
│   ├── _data/
│   │   └── kb-articles.yml        # ★ 知识库唯一数据源
│   ├── kb/                        # 知识库文章目录
│   │   ├── overview.md            # 建筑自控总览
│   │   ├── heating-cooling-overview.md  # 冷热源总览
│   │   ├── sensors.md             # 暖通传感器
│   │   ├── actuators.md           # 暖通常用执行器
│   │   ├── control-loop.md        # 控制回路
│   │   ├── algorithms.md          # 控制算法
│   │   ├── modbus.md              # MODBUS 通信协议
│   │   ├── refrigeration-cycle.md # 制冷循环基础
│   │   ├── refrigerants.md        # 制冷剂与工质
│   │   ├── refrigeration-equipment.md  # 制冷设备
│   │   ├── refrigeration-systems.md    # 制冷与热泵系统
│   │   └── boilers.md             # 锅炉设备
│   ├── hvac/tools/
│   │   └── hydraulic-calc.html    # 水力计算工具（纯 HTML）
│   ├── CNAME                      # 自定义域名
│   └── favicon.svg                # 网站图标
└── themes/cimu-kb/                # 自研 cimu-kb 主题
    ├── _config.yml                # 主题配置
    ├── layout/
    │   ├── layout.ejs             # 全局布局骨架
    │   ├── index.ejs              # 首页模板
    │   ├── tools.ejs              # 工具页模板
    │   └── _partial/
    │       ├── header.ejs         # 导航栏
    │       ├── footer.ejs         # 页脚
    │       ├── sidebar.ejs        # 侧边栏树
    │       ├── breadcrumb.ejs     # 面包屑
    │       └── search-overlay.ejs # 搜索面板
    ├── source/
    │   ├── css/style.css          # 全局样式
    │   ├── js/
    │   │   ├── nav-tree.js        # 侧边栏 + KB 切换
    │   │   ├── toc.js             # 文章目录
    │   │   └── search.js          # 本地搜索
    │   └── fonts/                 # 本地化字体文件
    └── ...
```

---

## 🚀 本地开发与部署

### 第 1 步：安装依赖

```bash
# 进入项目目录
cd hvac-hexo

# 安装所有依赖
npm install
```

### 第 2 步：本地预览

```bash
# 启动 Hexo 开发服务器
npm run server
# 或
hexo server

# 访问 http://localhost:4000 预览
```

### 第 3 步：生成静态文件

```bash
# 清理缓存并生成
npm run clean
npm run build
# 或
hexo clean
hexo generate

# 生成的静态文件在 public/ 目录
```

---

## 📤 部署方式

### 阿里云 ESA Pages（当前部署方式）

1. **仓库与分支**
   - 仓库：`kingfps007/hvac-lab`（GitHub）
   - 分支：`main`

2. **ESA 配置**
   - 项目类型：静态站点
   - 函数文件路径：留空（纯静态站）
   - 静态资源目录：`public`
   - 构建命令：`npm install && npm run build`
   - Node.js 版本：22.x
   - 自定义域名：`hvac.cimugarage.cn`

3. **自动部署**
   - 修改代码后直接 `git push` 到 `main` 分支
   - ESA Pages 会自动构建并部署

---

## ✏️ 日常编辑流程

### 编辑知识库

1. **知识库内容管理**
   - 所有知识库结构与文章列表在 `source/_data/kb-articles.yml` 统一管理
   - 修改文章内容：直接编辑 `source/kb/` 目录下的 Markdown 文件

2. **本地预览**
   ```bash
   hexo server
   # 访问 http://localhost:4000
   ```

3. **提交并部署**
   ```bash
   git add .
   git commit -m "更新知识库内容"
   git push
   # ESA Pages 自动构建
   ```

---

## 🎨 开发经验与踩坑记录

### 核心架构设计原则

1. **数据驱动**：`source/_data/kb-articles.yml` 是唯一真相源
   - 修改知识库结构只改这一个文件
   - 自动同步导航栏、侧边栏、首页、面包屑

2. **路径格式注意事项**
   - YAML 中路径带前导 `/`（如 `/kb/sensors.html`）
   - Hexo 的 `page.path` 不带前导 `/`（如 `kb/sensors/index.html`）
   - **任何路径比较必须先 `replace(/^\//, '')` 去掉前导斜杠！**

### CSS 布局硬规则

1. **三栏知识库布局**
   - 使用 `height` 而不是 `max-height` 填满视口
   - 父容器必须有 `min-height` 让短内容撑满
   - 页脚放在 `.kb-main` 内部，用 `margin-top: auto` 自动推底
   - 页脚绝不能在 `.kb-layout` 外部（会推挤 sidebar/TOC）

2. **首页分栏**
   - 桌面：3 列，平板：2 列，手机：1 列
   - 禁止使用 `repeat(auto-fill, minmax(...))`（会溢出或列数不可控）

3. **工具页字体**
   - 编号列表用手动 `<p>1. xxx</p>`，不要用 `<ol><li>`
   - `li::marker`、`strong` 需显式继承字体和颜色

### Git 与部署经验

1. **Git 推送问题**
   - Windows PowerShell 中文路径操作易出问题
   - `git push` 退出码不可靠，要实际看输出内容是否成功
   - 代理后终端连不上 GitHub：执行 `git config --global http.proxy http://127.0.0.1:7890`

2. **Token 安全（重要！）**
   - **绝不在 URL 中嵌入 Token**（`git remote -v` 会明文显示且进入历史）
   - 推荐使用 SSH 密钥：`git remote set-url origin git@github.com:USER/REPO.git`
   - 撤销泄露的 Token：GitHub → Settings → Personal access tokens → Revoke
   - 重建干净历史：`git checkout --orphan clean-main` + `git branch -D main` + `git push --force`

3. **沙箱限制**
   - 本地沙箱无 Node.js，无法运行 `hexo generate`
   - 验证靠阅读源码，实际构建靠 ESA Pages 自动处理

4. **编码陷阱**
   - PowerShell `Set-Content` 默认带 UTF-8 BOM，别用它写文件！
   - 用 `Out-File -Encoding utf8`（注意是 `utf8` 不是 `utf-8`）

### 深色模式实现

- 首次访问：`matchMedia` 跟随系统
- 手动切换后：`localStorage` 持久化
- 移动端悬浮按钮必须有独立深色模式覆盖

---

## 📝 知识库内容开发

### 添加新文章

1. 在 `source/_data/kb-articles.yml` 中添加文章条目
2. 在 `source/kb/` 目录创建对应 Markdown 文件
3. 在 Front Matter 中设置 `type: kb` 和 `layout: page`

### Front Matter 示例

```yaml
---
title: 文章标题
type: kb
layout: page
order: 10  # 排序权重
---
```

### KaTeX 数学公式

支持 LaTeX 公式渲染：
- 块级公式：`$$ ... $$`
- 行内公式：`$ ... $`

---

## 📜 项目规则与 AI 协作

本项目使用 Trae 规则系统沉淀经验，避免每次重复说明：

### 规则文件

| 文件 | 作用 |
|------|------|
| `.trae/rules/project_rules.md` | 项目特定规则（架构、命名、禁忌、文件联动、检查清单） |
| `.trae/rules/meta_rules.md` | 通用元规则（AI 工作流、规则更新、跨对话记忆） |

### 何时该更新规则

- 发现新的踩坑 → 补充 `project_rules.md`
- 用户纠正了 AI → 在 `project_rules.md` 添加禁忌项
- 改变了工作流习惯 → 更新 `meta_rules.md`
- 完成新功能 → 在 `project_rules.md` 联动表添加

### 完整流程

详见 `project_rules.md` 中的「十一、更新完成检查清单」和 `meta_rules.md` 中的「六、规则维护工作流」。

---

## 🔧 常用命令

```bash
# 清理缓存
hexo clean

# 生成静态文件
hexo generate

# 启动本地服务器
hexo server

# 新建页面
hexo new page "页面标题"
```

---

## ⚠️ 注意事项

1. **水力计算工具** (`hydraulic-calc.html`) 是纯 HTML 文件，Hexo 不会渲染它（已在 `_config.yml` 的 `skip_render` 中配置）
2. **知识库页面** 使用 `layout: page` 而非默认的 `post` 布局
3. **品牌色与深色模式** 通过 `themes/cimu-kb/source/css/style.css` 中的 CSS 变量统一控制
4. **每次更新后**，请运行项目规则中「十一、更新完成检查清单」逐项验证
