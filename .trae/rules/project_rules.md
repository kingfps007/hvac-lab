# 此木暖通车间 · 项目规则（Trae 设置加载）

> 📚 **本文件**：项目特定的架构、约定、踩坑  
> 🔗 **关联文件**：[meta_rules.md](file:///c:/Users/King/Desktop/260528hvac-hexo-trea/.trae/rules/meta_rules.md)（AI 通用工作流元规则）  
> ⚠️ **AI 必须先读**：用户偏好 + 本文件 + meta_rules.md 后再开始工作

## 一、项目元信息
- 域名：hvac.cimugarage.cn
- 仓库：kingfps007/hvac-lab（GitHub，已重置为干净历史）
- 部署：GitHub → 阿里云 ESA Pages（main 分支自动构建）
- ESA 配置：项目的函数文件路径必须留空（纯静态站），静态资源目录填 `public`
- 构建配置：`npm install` → `npm run build` → 输出 `public/`
- Git 远程：使用 SSH 或临时 PAT 输入，**绝不在仓库文件中保存 Token**

---

## 二、核心架构

### 数据驱动唯一真相源
```
source/_data/kb-articles.yml  ← ★ 改 KB 结构只改这一个文件
  ├── header.ejs     (导航栏下拉菜单)
  ├── sidebar.ejs    (左侧目录树 + KB 切换器)
  ├── breadcrumb.ejs (面包屑)
  └── index.ejs      (首页知识库栏目)
```

### 页面类型路由
```
page.type === 'kb'    → 三栏（sidebar + 正文 + TOC）
page.type === 'tools' → 全宽（hydraulic-calc.html）
page.type === 'index' → 首页
page.type === 'about' → 关于页
```

### 关键路径格式坑
```
kb-articles.yml 中路径: /kb/sensors.html        ← 带前导 /
Hexo page.path:          kb/sensors/index.html  ← 不带前导 /
```
**任何路径比较必须先 `replace(/^\//, '')` 去掉前导斜杠再比较！**

---

## 三、CSS 架构硬规则

### 知识库三栏布局
```css
.kb-layout { display: flex; min-height: calc(100vh - nav - crumb); align-items: stretch; }
.kb-sidebar { position: sticky; top: calc(nav + crumb); height: calc(100vh - nav - crumb); overflow-y: auto; }
.kb-toc     { position: sticky; top: calc(nav + crumb); height: calc(100vh - nav - crumb); overflow-y: auto; }
.kb-main    { flex: 1; display: flex; flex-direction: column; }
.kb-main .footer { margin-top: auto; }
```
关键点：
- 用 `height` 不用 `max-height`（填满视口）
- 父容器必须有 `min-height`（短内容撑满）
- 页脚放在 `.kb-main` 内部，`margin-top: auto` 自动推底
- 页脚绝不在 kb-layout 外部（会推挤 sidebar/TOC）

### 阅读进度条
```css
top: calc(var(--nav-height) + var(--crumb-height));
```
不是 `top: var(--nav-height)`！（面包屑占 29px）

### 导航栏
- 固定 `position: fixed`，无滚动动画，无 `nav-hidden` 类
- 字重 `font-weight: 700` 仅限 `.nav-links`，不要全站加粗
- 下拉菜单字体 `font-weight: 600`（不影响面包屑/侧边栏/文章）

### 首页分栏
```css
.kb-columns { grid-template-columns: repeat(3, 1fr); }                /* 桌面 3 列 */
@media (max-width: 1024px) { .kb-columns { repeat(2, 1fr); } }       /* 平板 2 列 */
@media (max-width: 768px)  { .kb-columns { 1fr; } }                  /* 手机 1 列 */
```
**禁止** `repeat(auto-fill, minmax(_____))`（会溢出或列数不可控）

### 工具页字体
- 编号列表用手动 `<p>1. xxx</p>`，不要用 `<ol><li>`
- `li::marker`、`strong` 需显式 `font-family: inherit; font-size: inherit; color: inherit;`
- 原因：`<ol>` 导致浏览器给数字/加粗单独套默认 sans-serif 字体

### 移动端悬浮按钮（深色模式）
```css
[data-theme="dark"] .sidebar-toggle { border-color: var(--primary-light); box-shadow: 0 2px 8px rgba(0,0,0,0.5); }
```
必须有独立深色模式覆盖，否则按钮和背景融成一片

---

## 四、面包屑路径匹配规则（breadcrumb.ejs）

```js
// 关键：YAML 路径去掉前导 / 后与 page.path 比较
var artBase = art.path.replace(/\.html$/, '').replace(/\/$/, '').replace(/^\//, '');
var pageBase = page.path.replace(/\.html$/, '').replace(/\/$/, '');

// 匹配：完全相等 或 加 /index 相等
if (pageBase === artBase || pageBase === artBase + '/index')
```

遍历时用 `if (currentKb) return;` 提前跳出，避免后续遍历覆盖正确结果。

---

## 五、nav-tree.js KB 切换规则

```js
var norm = function(p) { return p.replace(/\/$/, '').replace(/\.html$/, '').replace(/^\//, ''); };
```
1. 先精确匹配（`cn === norm(p) || cn === norm(p) + '/index'`）
2. 再兜底包含匹配（跳过 overview 短路径避免 `kb/` 误匹配 `kb/hc/`）
3. 匹配成功后更新 `currentKbLabel` 文本 + 切换 sidebar 树 + 高亮 dropdown

---

## 六、命名规范
- 知识库简称：建筑自控、冷热源（**禁加"知识库"后缀**）
- 总览页标题：建筑自控总览 / 冷热源总览
- 站点描述：暖通空调（HVAC）领域的个人开源知识库与工程工具集
- 禁止使用"隶属""品牌""商业"等词

---

## 七、文件修改联动
| 操作 | 改哪些文件 |
|------|----------|
| 新增/删除/改名 KB 文章 | **仅** `source/_data/kb-articles.yml` |
| 修改导航栏分类 | `kb-articles.yml` 的 sections |
| 总览页内容 | `source/kb/overview.md` 或 `heating-cooling-overview.md` |
| 总览页内链 | **必须** `./name.html`（建筑自控）或 `../name.html`（冷热源） |
| 品牌色/深色模式 | `style.css` 的 CSS 变量 |
| 页脚 | `footer.ejs` + `hydraulic-calc.html` |
| **新增第三方 JS 库** | 下载到 `themes/cimu-kb/source/lib/<name>/`（Hexo 会自动复制到 `public/lib/<name>/`） |
| **任何功能/样式更新** | **必须同时更新** `README.md` 的「更新记录」章节，添加日期、版本号和简要说明 |
| **任何功能/样式更新** | **必须同时更新** `source/about/index.md` 的「更新记录」章节（与 README 同步） |
| **修改规则/约定** | **必须同时更新** `.trae/rules/project_rules.md` 和 `meta_rules.md` |

---

## 八、禁止事项
- ❌ KB 文章列表硬编码到模板中
- ❌ 给 hydraulic-calc.html 加导航菜单/主题切换/搜索/汉堡按钮
- ❌ PowerShell `Set-Content` 写文件（UTF-8 BOM 陷阱）
- ❌ 总览页用 `./name/` 格式链接（必须 `./name.html`）
- ❌ 页脚放 kb-layout 外部
- ❌ `repeat(auto-fill, minmax(...))` 做分栏
- ❌ 路径比较不 strip 前导 `/`
- ❌ Token 写入仓库文件（.git/config 也不行）
- ❌ 删除规则条目而不留废弃标记
- ❌ **使用公共 CDN 加载 JS/CSS（jsdelivr / unpkg / cdnjs / bootcss 等）**——所有第三方库必须下载到 `themes/cimu-kb/source/lib/` 或 `source/assets/lib/` 本地化，通过 ESA Pages 加速。**反例**：`https://cdn.jsdelivr.net/npm/katex@0.16.9/...`；**正例**：`/lib/katex/katex.min.js`（来自 `themes/cimu-kb/source/lib/katex/`）。原因：CDN 抽风/被墙直接影响站点可用性，且违反隐私可控原则。
- ❌ 关于页（以及 type=page 的文章）出现项目编号圆点——`<ul>`/`<ol>` 必须 `list-style: none; padding-left: 0;`
- ❌ **正文（包括 KB 文章）禁止使用 `-` 列表编号**——改用 `（1）（2）` 论文格式（与 GB/T 7713 等学术规范一致）。hexo-renderer-marked 对 `- ` 列表渲染为 `<ul><li>`，且 CSS `padding-left: 2em` 会被多个浏览器加上 list-style marker；改为 `（1）` 后圆点完全消失。如需缩进可用 `&emsp;`。
- ❌ **markdown 块级公式用多行 `$$\n...\n$$` 包裹**——marked 渲染时插入 `<br>` 把 `$$` 切成独立文本节点，客户端 fixDisplayMath 正则 `\$\$([\s\S]+?)\$\$` 匹配失败。**必须单行** `$$公式$$`。
- ❌ **markdown 块级公式只写开头 `$$` 不写结尾**（v4.4.4 教训：12 个公式都缺结尾）——fixDisplayMath 正则要求 `$$` 必须成对出现。**所有独立公式必须 `$$<formula>$$` 双侧包裹**，可用 `Grep '^\$\$[^$]+$'` 扫描找出无结尾的（注意要避免匹配含 `$$` 中间的情况，可用 `^\$\$[^$]+$` 行级正则）。
- ❌ **markdown 表格内使用半角 `~`**（如 `+5~10%`）——marked GFM 扩展可能把 `~` 解析为 `<del>` 删除线，破坏表格内容。**改用全角 `～`（U+FF5E）** 或中文 `至`。
- ❌ **markdown 出现 `**xx$yy$**` 加粗紧贴 LaTeX 公式**——marked 的 strong 配对在 `$` 字符存在时会失败，整段原样输出。两种解法：（1）改为 `**xx** $yy$` 拆开加粗与公式；（2）依赖 `layout.ejs` 的 `fixUnmatchedBold` 客户端 JS 兜底（仅支持 `**XX$YY$**` 单个公式模式）。
- ❌ **KaTeX 公式禁止包含 CJK 字符**（v4.4.7 教训）——KaTeX 解析器不支持 CJK 字符，无论 `\text{中文}` 还是数学模式直接写中文均报红色 `katex-error`。`unicodeText: true` 无效（KaTeX 无此选项）。**正确做法**：（1）含大量中文的公式改用 HTML/CSS `.formula-box` + `.formula-frac` 渲染（见 `style.css`）；（2）CJK 下标改英文下标 + 文字说明（如 `Q_{得热}`→`Q_{gain}` 为得热）；（3）`style.css` 的 `.katex, .katex *` 字体列表仍需含中文 fallback 确保 CJK 字符有 glyph。
- ❌ **KaTeX 公式禁止使用 `\underbrace{...}_{...}` 等 amsmath 扩展命令**（v4.4.8 教训）——本地 KaTeX 0.16 默认**不含 amsmath 扩展包**，`\underbrace` 会抛错并兜底为红色明文（与 CJK 错误表现一致）。**正确做法**：（1）**不要**用 `\underbrace` 在公式下方写注释——改用「公式 + 公式后文字说明」分两行；（2）其他需要 amsmath 的命令（`\underset`、`\stackrel`、`\sideset` 等）同理禁止使用；（3）如确需 amsmath 命令，必须先在 KaTeX 渲染时调用 `katex.render(... , {trust: true})` 或在页面顶部添加 `<script>window.katex = ...; katex.__proto__.macros = {ams: '\\require{amsmath}'}</script>`（但 ESA 部署有体积限制，不推荐）。
- ❌ **页面默认主题跟随 `prefers-color-scheme`**（v4.4.6 教训）——Windows 11 24H2 把「应用模式」和「系统主题」分开设置，`prefers-color-scheme` 反映应用模式，导致应用模式深色 + 系统主题浅色时页面默认深色，用户预期不符。**主题切换**必须显式控制：只读 `localStorage.theme`，**不要**用 `prefers-color-scheme` 自动跟随；首次访问默认 CSS 浅色。
- ✅ **关键公式必须用 `\boxed{}` 包裹**——`\boxed{}` 给公式加边框，视觉上突出核心/关键公式，让读者一眼区分哪些是必须记住的、哪些只是推导过程。适用于各章节最重要的公式（如送风量、冷负荷系数、热负荷、散热器效率等）。

---

## 九、部署与调试
- 阿里云 ESA：项目类型选静态站点，函数文件路径留空
- ESA 配置文件：`esa.jsonc`（优先）或 `esa.json`
- 定位 ESA 构建失败：看 `函数文件路径` 是否被意外填写
- Hexo 路径格式：`kb/sensors/index.html`（page.path）、`/kb/sensors/`（permalink）
- 沙箱 `hexo generate` 不可用（无 Node），靠 ESA 自动构建验证

---

## 十、Git 配置
- **绝不在 URL 中嵌入 Token**（`git remote -v` 会明文显示并进入历史）
- 推荐使用 SSH 密钥：`git remote set-url origin git@github.com:USER/REPO.git`
- 或每次推送时输入 PAT：`git push` 时按提示输入 Username + Token
- 代理后终端不能连 GitHub：执行 `git config --global http.proxy http://127.0.0.1:7890`
- Clash 默认端口 7890，V2Ray 默认端口 10809
- 取消代理：`git config --global --unset http.proxy`

---

## 十一、更新完成检查清单

**每次完成任何功能/样式/内容更新后，必须逐项检查以下项目，并在给用户的回答末尾附上本清单：**

### 📝 文档更新检查
- [ ] **README.md 更新记录**：已在「更新记录」章节添加当前日期、版本号和简要说明
- [ ] **版本号递增**：版本号已正确递增

### 🗂️ 相关文件同步检查
- [ ] **页脚修改**：如果修改了页脚，确认已同时更新 `footer.ejs` **和** `hydraulic-calc.html`
- [ ] **KB 结构修改**：如果修改了知识库结构，确认只改了 `source/_data/kb-articles.yml`
- [ ] **总览页链接**：如果修改了总览页内链，确认用的是 `./name.html`（建筑自控）或 `../name.html`（冷热源）
- [ ] **样式文件覆盖**：如果新增深色模式样式，确认有对应的 `[data-theme="dark"]` 覆盖规则

### 🔒 隐私安全检查
- [ ] **敏感信息扫描**：用 `Grep` 搜索 `ghp_/ghs_/gho_/ghu_/ghr_` 等 token 模式
- [ ] **Git 远程 URL**：确认 `git remote -v` 不含 token
- [ ] **Git 历史**：重要隐私数据用 `git filter-repo` 清理或重建仓库

### 🚀 Git 提交与推送检查
- [ ] **Git 状态**：执行 `git status`，确认所有修改的文件已添加
- [ ] **Git 提交**：已执行 `git add` 和 `git commit`，提交信息清晰描述修改内容
- [ ] **Git 推送**：已执行 `git push`，代码已成功推送到 GitHub 的 main 分支

### ✅ 验证与收尾
- [ ] **本地预览**：（如有条件）运行 `npm run server` 验证修改效果
- [ ] **检查清单**：确认本清单所有项目都已勾选完毕，且已在给用户的回答末尾附上

---

## 十二、紧急情况处理

### Token 泄露
1. **立即撤销**：GitHub → Settings → Personal access tokens → Revoke
2. **重建历史**：`git filter-repo` 或 `git checkout --orphan` + `git branch -D old`
3. **强制推送**：`git push --force --all`
4. **更新远程**：从远程 URL 中移除 token，改用 SSH 或临时输入

### 部署失败
1. 检查 ESA 控制台「函数文件路径」是否被意外填写
2. 检查 `_config.yml` 中 `skip_render` 是否正确
3. 查看 ESA 构建日志，定位具体错误
