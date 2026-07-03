# 此木暖通车间 · CIMU HVAC LAB

> 个人开源站点 — 暖通专业知识与工程工具集成平台  
> 域名：[hvac.cimugarage.cn](https://hvac.cimugarage.cn)  
> 仓库：[kingfps007/hvac-lab](https://github.com/kingfps007/hvac-lab) (Public)

---

## 🎯 项目简介

此木暖通车间是一个面向暖通空调（HVAC）领域的**个人开源知识库**与**工程工具集**。包含两大部分：

- **建筑自控**：传感器、执行器、控制回路、控制算法、通信协议
- **冷热源**：制冷循环、制冷剂、制冷设备、制冷系统、锅炉

外加一个实用的**水力计算工具**（不等温降法）。

---

## 🛠️ 技术栈

| 层 | 技术选型 | 说明 |
|----|---------|------|
| 框架 | Hexo 7.3.0 | 静态站点生成器 |
| 主题 | cimu-kb（自研） | EJS 模板 + 自定义 CSS |
| 数据层 | `source/_data/kb-articles.yml` | 集中管理知识库结构 |
| 搜索 | hexo-generator-searchdb | 客户端实时搜索（debounce 300ms） |
| SEO | Open Graph + JSON-LD | 社交分享 + Schema.org |
| 数学公式 | KaTeX | LaTeX 公式渲染 |
| 部署 | GitHub → 阿里云 ESA Pages | push main 自动构建 |
| AI 协作 | Trae 规则系统 | 自动加载项目 + 元 + 扩展规则 |

---

## 📁 目录结构

```
hvac-hexo/
├── _config.yml                    # Hexo 主配置
├── .editorconfig                  # 编码规范
├── .gitignore                     # Git 忽略文件
├── package.json                   # 依赖配置
├── README.md                      # 项目说明与更新记录（本文件）
├── SETUP_GUIDE.md                 # 开发搭建指南与经验总结
├── .trae/                         # Trae 规则目录（AI 自动加载）
│   └── rules/
│       ├── project_rules.md       # 项目特定规则（架构/命名/禁忌/检查清单）
│       ├── meta_rules.md          # 通用元规则（AI 工作流/规则更新/跨对话记忆）
│       ├── extensibility.md       # 扩展性架构（组件注册/资源加载/可插拔）
│       ├── comment_workflow.md    # 评论系统工作流（ESA 函数 + KV）
│       └── media_workflow.md      # 多媒体资源工作流（图/视/PDF/性能优化）
├── source/
│   ├── index.md                   # 首页
│   ├── about/index.md             # 关于页
│   ├── _data/
│   │   └── kb-articles.yml        # ★ 知识库唯一数据源
│   ├── assets/                    # ★ 静态资源根目录（详见 media_workflow.md）
│   │   ├── images/                # 图片（按主题分子目录）
│   │   ├── videos/                # 视频
│   │   ├── pdfs/                  # PDF 文档
│   │   └── interactive/           # 交互式资源（焓湿图/曲线等）
│   ├── kb/                        # 知识库文章（12 篇）
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
│   │   └── hydraulic-calc.html    # 水力计算工具
│   ├── CNAME                      # 自定义域名
│   └── favicon.svg                # 网站图标
├── themes/cimu-kb/                # 自研 cimu-kb 主题
│   ├── _config.yml                # 主题配置
│   ├── layout/
│   │   ├── layout.ejs             # 全局骨架
│   │   ├── index.ejs              # 首页
│   │   ├── tools.ejs              # 工具页面
│   │   └── _partial/
│   │       ├── header.ejs         # 导航栏
│   │       ├── footer.ejs         # 页脚
│   │       ├── sidebar.ejs        # 侧边栏树
│   │       ├── breadcrumb.ejs     # 面包屑
│   │       └── search-overlay.ejs # 搜索面板
│   └── source/
│       ├── css/style.css          # 全局样式
│       ├── js/
│       │   ├── nav-tree.js        # 侧边栏 + KB 切换
│       │   ├── toc.js             # 文章目录
│       │   ├── search.js          # 本地搜索
│       │   └── components/        # ★ 可插拔组件（详见 extensibility.md）
│       │       ├── registry.js    # 组件注册表
│       │       ├── psychrometric/ # 焓湿图（待实现）
│       │       ├── curves/        # 性能曲线（待实现）
│       │       ├── image-viewer/  # 图片查看器（待实现）
│       │       ├── pdf-viewer/    # PDF 阅读器（待实现）
│       │       ├── video-player/  # 视频播放器（待实现）
│       │       ├── comment/       # 评论组件（待实现）
│       │       └── _template/     # 新组件模板
│       └── fonts/                 # 本地化字体
└── functions/                     # ★ ESA 云函数（待启用，详见 comment_workflow.md）
    └── (留空，等开启评论功能时创建)
```

---

## 🎨 主题架构

### 页面类型路由

| `page.type` | 布局 | 特征 |
|-----------|------|------|
| `kb` | 三栏 | 侧边栏 + 正文 + 目录 + 进度条 |
| `tools` | 全宽 | 独立内容区 |
| `index` | 卡片 | hero 头图 + 工具/知识库网格 |
| `about` | 单栏 | 居中窄版 |
| 其他 | 单栏 | `.container` 通用 |

### 设计体系

```
主色 #B05A32  深色 #3D2820  正文 #1C1C1C  背景 #F8F4F0
字体 Tinos (拉丁) + 宋体 (中文)，本地嵌入，无外链
深色模式 localStorage 优先，matchMedia 系统跟随
响应式：1024px 平板 / 768px 手机
```

### 构建与部署

```bash
npm install
npm run server        # 本地预览 http://localhost:4000
npm run build         # 生成 public/
```

```
本地编辑 → git push origin main → 阿里云 ESA Pages → hvac.cimugarage.cn
```

---

## 📚 知识库结构

集中定义在 `source/_data/kb-articles.yml`，驱动 header / sidebar / index 三模板自动渲染。

### 建筑自控（`/kb/`）

| 层级 | 文章 |
|------|------|
| 感知与执行层 | 暖通传感器、暖通常用执行器 |
| 控制与算法层 | 控制回路、控制算法 |
| 通信协议层 | MODBUS 通信协议 |

### 冷热源（`/kb/hc/`）

| 层级 | 文章 |
|------|------|
| 循环与工质层 | 制冷循环基础、制冷剂与工质 |
| 设备与系统层 | 制冷设备、制冷与热泵系统 |
| 热源设备层 | 锅炉设备 |

---

## 📋 修改联动规则

| 操作 | 只需改哪些文件 |
|------|-------------|
| 新增/删除/改名 KB 文章 | **仅** `source/_data/kb-articles.yml` |
| 修改总览页内容 | `source/kb/overview.md` 或 `heating-cooling-overview.md` |
| 改品牌色 / 深色模式 | `style.css` |
| 改页脚 | `footer.ejs` + `hydraulic-calc.html` |
| **新增图片/视频/PDF** | 复制到 `source/assets/...` + Markdown 引用 |
| **新增交互组件** | 复制 `components/_template/` + 改 `registry.js` |
| **新增评论** | 启用 ESA 函数 + 配置 `functions/comment/` |
| **任何功能/样式更新** | **必须同步更新** `README.md` 更新记录 + 检查清单 |
| **修改项目规则/约定** | **必须同步更新** `.trae/rules/project_rules.md` |
| **修改 AI 工作流/通用方法** | **必须同步更新** `.trae/rules/meta_rules.md` |
| **新增扩展架构/资源规则** | **必须同步更新** `.trae/rules/extensibility.md` |
| **新增评论相关** | **必须同步更新** `.trae/rules/comment_workflow.md` |
| **新增多媒体相关** | **必须同步更新** `.trae/rules/media_workflow.md` |

### 禁止事项

- ❌ 不把知识库列表改回硬编码
- ❌ 不给 hydraulic-calc.html 加导航/搜索/主题切换
- ❌ 不用 PowerShell Set-Content 写文件（UTF-8 BOM 陷阱）
- ❌ 不用 `./name/` 格式写总览页内部链接（必须 `./name.html`）
- ❌ 页脚不用"隶属""品牌"等商业词汇
- ❌ 不要把 Token 写入 .git/config 或仓库文件
- ❌ 不要把资源散落在 `themes/` 下（必须放 `source/assets/`）
- ❌ 不要绕过 `components/registry.js` 引入组件

---

## 📦 更新记录

**2026-07-01** - v4.5
- **依赖清理**：删除 `package.json` 中未使用的 `hexo-theme-fluid`、`hexo-theme-next`（项目自研 `cimu-kb` 主题，二者从未启用），`package-lock.json` 通过 `npm install --package-lock-only` 同步
- **`source/assets/` 目录骨架**：新增 `README.md` 说明媒体资源规范（图片/视频/PDF/交互），子目录按需创建避免空目录噪音
- **`components/` 组件骨架**：新增 `registry.js`（注册表入口 + 注释占位 6 个计划组件）+ `_template/`（可复用的 `index.js` / `styles.css` / `README.md` 三件套）+ 总 `README.md` 加载机制说明
- **无功能代码改动**：本次为架构占位与依赖治理，运行时行为零变化

**2026-06-05** - v4.4.10
- **二次回风教材公式链完整推导**（hvac-ac.md 新增 §4.3.0）：从房间全冷负荷 $Q$ 出发，按**能量守恒 → 质量守恒**链式推导教材中 5 个核心公式——$G_S = Q/(h_R - h_O)$ → $G_O = Q/(h_R - h_L)$ → $G_{R2} = G_S - G_O$ → $G_{R1} = G_O - G_N$ → $Q_{ch} = G_O \cdot (h_{C_1} - h_L)$
- **关键澄清**：$G_O \cdot (h_R - h_L)$（房间角度等效焓降）vs $G_O \cdot (h_{C_1} - h_L)$（冷盘管实际冷量），二者因新风附加不同
- **数值验证**：$Q = 35.83$ kW → $G_S/G_O/G_{R2}/G_{R1}$ 依次验证 + $Q_{ch} = 47.60$ kW（含新风附加）差异分析

**2026-06-05** - v4.4.9
- **二次回风计算流程详细补充**（hvac-ac.md §4.3 扩 5 个子章节）：（1）风量构成与质量守恒（$G_S = G_N + G_{R1} + G_{R2}$，$G_O = G_N + G_{R1}$）；（2）夏季工况送风温差决定混合点（能量守恒推导 $G_{R2}/G_S = (h_O - h_L)/(h_R - h_L)$）；（3）数值算例（$G_S = 10000$ kg/h，$G_{R2} = 4160$ kg/h，能量守恒验算误差 < 0.02%）；（4）冬季工况不可行分析（$G_{R2}/G_S > 1$ 无解，需要再热器配合）；（5）工程设计要点表（5 个经验参数）+ 节能对比（与一次回风 + 再热）

**2026-06-05** - v4.4.8
- **机械 vs 重力循环偏导数对比分析**（核心新增）：hvac-heating.md 新增 §2.4 章节，**多节点管网一般化偏导数模型** $H_p + H_{g,i} = S_i \cdot G_i^2$ → $\partial G_i/\partial H_p$、$\partial \sigma_i/\partial Q_i$ 推导；6 层双管系统数值算例（机械失调度 $\Sigma_M$ vs 重力失调度 $\Sigma_G$）；**4 个反向劣势场景**（停电、深度质调节、高层超压、极小流量）；工程选型表 + 调节性能/能耗投资对比
- **水力计算工具页 UX 改进**：（1）管径参考表默认展开（去掉 `display:none`）；（2）新增「系统场景选择器」——5 种预置场景（单管串联 / 双管异程 / 双管同程 / 单管跨越 / 高层双管失调 6 层），点按钮一键载入示例数据；（3）管径表 / 场景说明提示用户区分不同管段含义
- **水力计算工具页新增「阀门选型与调节指导」**：在结果区输出——最不利/最有利环路、阻力比判断（>1.5 必须装平衡阀）、按 DN 自动推荐阀门（温控阀 DN15 / 手动调节阀 DN20-25 / 静态平衡阀 DN32-50 / 蝶阀 DN65+）、流速异常提示、水泵选型参数（流量+扬程+1.2 安全系数）、推荐调节方式
- **水力计算工具页新增「八、机械 vs 重力循环偏导数对比」交互模块**：6 个模型参数（层数/层高/水泵压头/阻力系数/温差/密度差）+ 4 张输出表（各支路流量对比 / 系统失调度 Σ / 偏导数灵敏度 / 量化结论），实时计算并自动给出工程建议

**2026-06-05** - v4.4.7
- **KaTeX CJK 公式彻底修复**：KaTeX 解析器不支持 CJK 字符（无论 `\text{}` 还是数学模式均报红色明文）。v4.4.5 的 `unicodeText: true` 无效（KaTeX 无此选项），v4.4.7 初版去掉 `\text{}` 直接写中文也不行。**最终方案**：（1）含大量中文的公式改用 HTML/CSS `.formula-box` + `.formula-frac` 渲染（配比系数、ADPI）；（2）CJK 下标改英文下标 + 文字说明（`Q_{得热}`→`Q_{gain}` 等）；（3）layout.ejs 移除无效 `unicodeText: true`
- **影响文件**：hvac-ac.md（1 处 HTML 公式）、hvac-load.md（7 处英文下标替换）、hvac-air-distribution.md（1 处 HTML 公式 + 1 处下标替换）、style.css（新增 `.formula-box` / `.formula-frac`）

**2026-06-05** - v4.4.6
- **页面默认浅色（跟随用户显式选择，不跟随系统）**：去掉 layout.ejs 的 `prefers-color-scheme: dark` 检测和 `matchMedia change` 监听器。Windows 11 24H2 把「应用模式」和「系统主题」分开设置，`prefers-color-scheme` 反映应用模式而非系统主题——你电脑应用模式深色 + 系统主题浅色时页面会默认深色，不符合用户预期。**修复**：首次访问默认 CSS 浅色，只在用户**显式点过**主题按钮（`localStorage.theme === 'dark'`）时才深色。v4.4.4 的防白闪 inline 脚本同步精简
- **影响范围**：仅未访问过本站的新用户（localStorage 无 theme 记录）。已访问过的用户 localStorage 已有值，行为不变

**2026-06-05** - v4.4.5
- **`\text{中文}` 红色明文修复**：KaTeX 0.16+ 默认支持 Unicode text，但 `style.css` `.katex, .katex *` 字体列表只含西文，遇到 CJK 字符会抛错（throwOnError: false 兜底为红色 `katex-error` 文本）。**核心修复**：CSS 字体列表追加 `'Microsoft YaHei', 'PingFang SC', 'Hiragino Sans GB', 'SimHei', 'SimSun', '\5B8B\4F53'` 中文 fallback（覆盖 Windows/Mac/iOS/Android 默认中文字体）。同时 `katex.render` 和 `renderMathInElement` 加 `unicodeText: true` 显式启用 Unicode 字符模式
- **影响公式** 3 处：`hvac-ac.md:291`（配比系数）、`hvac-load.md:200`（总热负荷含 `\text{附加}\text{冷风}`）、`hvac-air-distribution.md:34`（ADPI 含 `\text{测点数}\text{总测点数}`）

**2026-06-05** - v4.4.4
- **12 个独立公式补结尾 `$$`**：refrigeration-cycle.md (4) / refrigeration-systems.md (1) / boilers.md (6) 中 `$$<formula>` 但缺结尾 `$$`，导致客户端 fixDisplayMath 正则 `\$\$([\s\S]+?)\$\$` 匹配失败。逐行末尾补 `$$` 后与 hvac-ac.md:70 送风量等"成功案例"形式一致
- **深色模式防首屏白闪**：`<head>` 顶部加同步 inline script（CSS 加载前执行），立即读 `localStorage.theme` 和 `prefers-color-scheme` 给 `<html>` 设 `data-theme="dark"`，彻底消除"白→黑"闪烁

**2026-06-05** - v4.4.3
- **"总览"加字范围回退**：仅首页 KB 列头部显示"建筑自控总览 / 冷热源总览 / 暖通空调总览"；KB 切换器下拉、切换器标签、当前 KB 名恢复为"建筑自控 / 冷热源 / 暖通空调"（文章目录侧栏树保留"总览"，因为 yml 数据 overview.title 本就含"总览"）
- **侧边栏当前文章高亮**：`nav-tree.js` 用 `normPath` 规范化路径比较，限定选择器到 `.tree a`，访问 `/kb/xxx.html` 时侧栏对应文章加 `current` class（背景色 + 强调色）
- **去掉黑白切换动画**：body 删除 `transition: background 0.3s, color 0.3s`，深浅色切换不再闪光
- **关于页/正文禁止圆点编号**：about 页 `body.about` 列表样式扩展（之前只 `body.page`），所有 `- ` 列表批量改 `（1）（2）` 论文格式（GB/T 7713 规范）
- **冷热源独立公式修复**：12 处多行 `$$\n...\n$$` 全部改为单行 `$$...$$`（marked 多行会插入 `<br>` 切断 `$$` 配对，导致客户端 fixDisplayMath 失败）
- **表格删除线修复**：hvac-load.md 表格内 13+ 处半角 `~` 改为全角 `～`（U+FF5E），避免 marked GFM 解析为 `<del>`
- **加粗紧邻公式修复**：layout.ejs 新增 `fixUnmatchedBold` 客户端 JS 兜底，处理 `**xx$yy$**` 模式（`$` 字符干扰 marked strong 配对），转 `<strong>xx</strong>$yy$`

**2026-06-05** - v4.4.2
- **深色模式链接对比度**：a 标签深色模式用 `#F0B894`（高对比），并去掉 `transition: color` 防止跳转瞬间白→黑闪
- **悬浮按钮高对比**：浅色模式图标文字用黑（#000），深色模式用纯白（#fff）
- **公式横向滚动**：`.katex-display` 加 `overflow-x: auto` + 触屏滑动，长公式不再丢失
- **公式西文字体**：`.katex` 强制 Tinos / Latin Modern Roman / Times New Roman
- **块级公式自动渲染**：客户端 JS 手动处理 `$$...$$`（marked 不识别时由 KaTeX 直接渲染）
- **`**` 与公式紧邻修复**：客户端在 `</strong>$` 之间插入零宽空格，避免公式被吞
- **首页/侧边栏加"总览"**：建筑自控显示"建筑自控总览"，冷热源显示"冷热源总览"，暖通显示"暖通空调总览"
- **删除所有代码块画图**：11 篇文章中的 mermaid 和 ASCII 字符图全部清除，改为纯文字 + 提示"待后期插入矢量图"

**2026-06-05** - v4.4.1
- **修复 404**：暖通空调 KB 路径从子目录式 `/kb/hvc/xxx.html` 改为扁平式 `/kb/hvac-xxx.html`，与项目统一风格一致（建筑自控、冷热源也是扁平式）
- **去除应试教辅口吻**：所有 v4.4 暖通文章删除"核心考点表"、"考试占比"、"考试提示"、"公式速记"等内容
- 改为工程原理导向：保留概念、公式、计算过程，添加"设计含义""为什么"等原理性段落

**2026-06-05** - v4.4
- **新增「暖通空调」知识库**（id: hvac）：覆盖考试四大题型（分析/画图/水力/设计）
- 第一章 绪论：HVAC 任务、室内外设计参数、不保证率 0.4%、规范体系
- 第二章 热湿负荷：得热 ≠ 负荷（辐射延迟）、冷负荷系数、房间/系统负荷、热湿比、最小新风
- 第三章 供暖系统：散热器放热系数、热水供暖、高层分区、热计量、**不等温降水力计算**、辐射供暖
- 第四章 空气调节：**质调节分区焓湿图**、**二次回风**、**变风量节能**、FCU+新风、VRV 配比、热回收 VRV、水环热泵
- 第五章 气流组织：典型模式、ADPI 评价、风口选型
- 第六章 防排烟：烟气特性、机械排烟量、加压送风余压（新增章节）
- 全部用 mermaid 画系统图与流程图，配核心公式表

**2026-06-05** - v4.3
- **KaTeX 完全本地化**：删除 jsdelivr CDN 引用，3 个 KaTeX 文件下载到 `themes/cimu-kb/source/lib/katex/`
- 全文零公共 CDN：所有 JS/CSS 全部走 ESA Pages 加速，保证速度与可访问性
- 关于页简化：去除项目编号圆点列表样式（`list-style: none`），去除重复介绍语
- 修复 `refrigeration-equipment.md` 中 `\dot{V}{actual}` 渲染异常，改用 `\dot V_{\text{actual}}`
- 修复 `refrigeration-cycle.md` 轴功率公式，补充 `\text{}` 包裹下标
- `project_rules.md` 新增"禁止公共 CDN"硬规则

**2026-06-04** - v4.2
- 关于页全面更新：补充 v3.0~v4.1 完整更新记录（原停留在 v2.8）
- 关于页新增"开源项目"章节（GitHub 仓库、CC BY-SA 4.0 许可、致谢）
- 调整关于页样式与 KB 文章保持一致（h2/h3 间距、列表缩进、链接下划线）
- 在 `project_rules.md` 增加"任何更新必须同步更新关于页"规则
- 修复 `style.css` 关于页 list 样式缺失的问题

**2026-06-04** - v4.1
- 新增 `.trae/rules/extensibility.md`：扩展性架构（组件注册、资源加载、可插拔）
- 新增 `.trae/rules/comment_workflow.md`：评论系统工作流（ESA 函数 + KV）
- 新增 `.trae/rules/media_workflow.md`：多媒体资源工作流（图片/视频/PDF/性能优化）
- 在 README.md 目录结构中加入 `source/assets/` 和 `themes/.../components/` 完整路径
- 完善修改联动规则：覆盖新增资源/组件/评论的修改流程
- 为未来扩展（焓湿图/曲线/视频/PDF/评论）提供标准化模板

**2026-06-04** - v4.0
- 仓库完全重置：删除 58 个旧提交，1 个干净提交重建历史
- 创建 `.trae/rules/meta_rules.md` 通用元规则
- 更新 `project_rules.md`：增加 meta_rules 引用、Token 安全章节、紧急情况处理
- 清理所有无关文件（RSS、GitHub Actions、旧主题配置、retrospectives）
- 撤销旧 GitHub Token（请确保已在 GitHub 设置中撤销）
- 修复 SETUP_GUIDE.md 中的隐私信息（code-server 地址）
- 新仓库无任何历史敏感数据
- 在 SETUP_GUIDE.md 中补充「项目规则与 AI 协作」章节
- 在 README.md 目录结构中标注 `.trae/` 是 AI 自动加载的规则目录

---

## 📖 文档索引

| 文档 | 作用 | 何时阅读 |
|------|------|---------|
| **本文件（README.md）** | 项目说明、目录结构、更新记录 | 第一次接触项目时 |
| **[SETUP_GUIDE.md](SETUP_GUIDE.md)** | 开发搭建指南、踩坑记录、完整经验 | 部署或遇到具体问题时 |
| **[.trae/rules/project_rules.md](.trae/rules/project_rules.md)** | 项目特定规则（架构、命名、禁止事项、文件联动、检查清单） | **AI 必须** / 人类开发者修改约定时 |
| **[.trae/rules/meta_rules.md](.trae/rules/meta_rules.md)** | 通用元规则（AI 工作流、规则更新、跨对话记忆） | **AI 必须** / 新会话开始时 |
| **[.trae/rules/extensibility.md](.trae/rules/extensibility.md)** | 扩展性架构（组件注册、资源加载、可插拔） | 实现新功能（焓湿图/曲线/视频）前 |
| **[.trae/rules/comment_workflow.md](.trae/rules/comment_workflow.md)** | 评论系统工作流（ESA 函数 + KV） | 启用评论功能前 |
| **[.trae/rules/media_workflow.md](.trae/rules/media_workflow.md)** | 多媒体资源工作流（图片/视频/PDF/性能优化） | 添加图片/视频/PDF 前 |

### 规则文件的双重价值

- **对 AI 助手**：Trae 会自动加载这些规则，新对话开始时 AI 就能"继承记忆"
- **对人类开发者**：这些规则是项目经验的沉淀，避免"猴子掰玉米"

### 规则文件加载优先级

新对话中 AI 应**按以下顺序**读取：
1. `meta_rules.md`（最优先：AI 行为准则）
2. `project_rules.md`（项目特定约束）
3. `extensibility.md` / `comment_workflow.md` / `media_workflow.md`（按任务需要）

---

## 💡 开发经验要点

### 核心设计原则
- **数据驱动**：`source/_data/kb-articles.yml` 是唯一真相源
- **路径归一化**：比较路径前必须先去掉前导斜杠
- **可插拔**：所有新功能通过 `components/registry.js` 接入
- **资源集中**：所有媒体资源在 `source/assets/`

### CSS 硬规则
- 使用 `height` 而非 `max-height` 填满视口
- 页脚放在 `.kb-main` 内部，用 `margin-top: auto` 推底
- 分栏禁用 `repeat(auto-fill, minmax(...))`

### Git 与部署
- **绝对不要**在 URL 中嵌入 Token（`git remote -v` 会暴露且进入历史）
- 推荐使用 SSH：`git remote set-url origin git@github.com:USER/REPO.git`
- ESA Pages 配置：函数文件路径留空（纯静态站），静态目录填 `public`
- 代理问题：`git config --global http.proxy http://127.0.0.1:7890`

### 历史清理
```bash
# 完全重建干净历史
git checkout --orphan clean-main
git commit -m "initial"
git branch -D main
git branch -m main
git reflog expire --expire=now --all
git gc --prune=now --aggressive
git push --force origin main
```
