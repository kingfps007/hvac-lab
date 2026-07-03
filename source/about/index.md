---
title: 关于
layout: page
type: about
---

## 此木暖通车间

暖通空调（HVAC）领域的个人开源知识库与工程工具集。

---

## 项目特色

（1）**数据驱动**：所有内容结构由 `kb-articles.yml` 统一管理，新增加文章只需改一处
（2）**开源协作**：源码、规则、内容全部开放，欢迎 Fork 与贡献
（3）**工程导向**：可调参数、可复制代码、可直接应用，不做空泛理论
（4）**持续演进**：从基础文章出发，逐步扩展到交互组件（焓湿图、曲线）、多媒体（视频、PDF）、评论系统

---

## 技术栈

（1）**框架**：Hexo 7.3.0 静态站点
（2）**主题**：自研 cimu-kb（EJS 模板 + 主题色 #B05A32）
（3）**搜索**：hexo-generator-searchdb 客户端实时搜索
（4）**公式**：KaTeX 渲染 LaTeX
（5）**部署**：GitHub → 阿里云 ESA Pages

---

## 开源项目

（1）**GitHub 仓库**：[kingfps007/hvac-lab](https://github.com/kingfps007/hvac-lab)
（2）**在线访问**：[hvac.cimugarage.cn](https://hvac.cimugarage.cn)
（3）**许可协议**：本站内容采用 [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/deed.zh) 许可；代码部分遵循 MIT

### 致谢

（1）[Hexo](https://hexo.io/) — 静态站点生成器
（2）[KaTeX](https://katex.org/) — 数学公式渲染
（3）[Tinos 字体](https://fonts.google.com/specimen/Tinos) — 衬线英文
（4）[阿里云 ESA Pages](https://www.aliyun.com/product/esa) — 静态站点托管

---

## 更新记录

### 2026-07-01 · v4.5 架构占位与依赖治理
（1）清理 `package.json` 未使用的 `hexo-theme-fluid`、`hexo-theme-next` 依赖，`package-lock.json` 同步
（2）新增 `source/assets/README.md` 说明媒体资源规范（图片/视频/PDF/交互）
（3）新增 `themes/cimu-kb/source/js/components/` 组件骨架：`registry.js`（注册表入口）+ `_template/`（index.js / styles.css / README.md 三件套）+ 总 README.md
（4）本次仅架构占位与依赖治理，运行时行为零变化

### 2026-06-05 · v4.4.10 二次回风教材公式链推导
（1）hvac-ac.md 新增 §4.3.0「从房间负荷出发的完整推导」——从 $Q$ 出发链式推导 5 个核心公式
（2）$G_S = Q/(h_R - h_O)$ → $G_O = Q/(h_R - h_L)$ → $G_{R2} = G_S - G_O$ → $G_{R1} = G_O - G_N$ → $Q_{ch} = G_O(h_{C_1} - h_L)$
（3）关键澄清：$G_O(h_R - h_L)$ vs $G_O(h_{C_1} - h_L)$ 的区别（房间角度 vs 实际冷盘管）
（4）数值验证 + 新风附加差异分析（$Q_{ch} > Q$）

### 2026-06-05 · v4.4.9 二次回风计算流程
（1）hvac-ac.md §4.3 扩 5 个子章节（4.3.1 ~ 4.3.5）
（2）核心公式：$G_{R2} = G_S - G_O = G_S - (G_N + G_{R1})$（质量守恒）；$G_{R2}/G_S = (h_O - h_L)/(h_R - h_L)$（能量守恒）
（3）数值算例 + 验算（$G_S = 10000$，$G_{R2} = 4160$）
（4）冬季工况不可行分析（$G_{R2}/G_S > 1$）
（5）工程设计要点表 + 节能对比

### 2026-06-05 · v4.4.8 偏导数对比分析 + 工具页增强
（1）hvac-heating.md 新增 §2.4「机械 vs 重力循环偏导数对比」：多节点管网一般化偏导数模型 $H_p + H_{g,i} = S_i \cdot G_i^2$ → $\partial G_i/\partial H_p$、$\partial \sigma_i/\partial Q_i$ 完整推导
（2）6 层双管系统数值算例（机械失调度 vs 重力失调度）+ 4 个反向劣势场景（停电 / 深度质调节 / 高层超压 / 极小流量）
（3）水力计算工具页 UX：管径表默认展开、5 种场景选择器、单按钮载入示例
（4）水力计算工具页新增「阀门选型与调节指导」：阻力比判断 + 按 DN 推荐阀门 + 水泵选型参数 + 调节方式
（5）水力计算工具页新增「八、偏导数对比」交互模块：6 参数输入 + 4 张输出表 + 实时量化结论

### 2026-06-05 · v4.4.7 KaTeX CJK 公式修复
（1）KaTeX 解析器不支持 CJK 字符（`\text{中文}` 和数学模式均报红色明文），`unicodeText: true` 无效
（2）含大量中文的公式改用 HTML/CSS `.formula-box` 渲染（配比系数、ADPI）
（3）CJK 下标改英文下标 + 文字说明（`Q_{得热}`→`Q_{gain}` 等 7 处）
（4）layout.ejs 移除无效 `unicodeText: true`，style.css 新增 `.formula-box` / `.formula-frac`

### 2026-06-05 · v4.4.6 主题默认策略
（1）页面默认浅色（不跟随 Windows 11 应用模式）：layout.ejs 去掉 `prefers-color-scheme: dark` 检测和 `matchMedia change` 监听
（2）首次访问默认 CSS 浅色，只在用户显式点过深色按钮（`localStorage.theme === 'dark'`）时才深色
（3）v4.4.4 防白闪 inline 脚本同步精简——仅在 saved === 'dark' 时设置 data-theme

### 2026-06-05 · v4.4.5 中文字体支持
（1）`\text{中文}` KaTeX 红色明文修复：style.css 字体列表追加中文 fallback（Microsoft YaHei / PingFang SC / SimHei / SimSun / 宋体），katex.render 和 renderMathInElement 加 unicodeText: true
（2）影响 3 处公式：hvac-ac.md:291（配比系数）、hvac-load.md:200（总热负荷）、hvac-air-distribution.md:34（ADPI）

### 2026-06-05 · v4.4.4 公式与首屏
（1）12 个独立公式补结尾 `$$`：refrigeration-cycle.md (4) / refrigeration-systems.md (1) / boilers.md (6) 中 `$$<formula>` 缺结尾 `$$`（fixDisplayMath 正则不匹配），全部补全
（2）深色模式防首屏白闪：`<head>` 顶部同步 inline script，CSS 加载前应用 `data-theme="dark"`，消除白→黑闪烁

### 2026-06-05 · v4.4.3 体验与渲染精细化
（1）"总览"加字范围回退：仅首页 KB 列头部显示"建筑自控总览 / 冷热源总览 / 暖通空调总览"；KB 切换器、当前 KB 名恢复为"建筑自控 / 冷热源 / 暖通空调"
（2）侧边栏当前文章高亮：访问 `/kb/xxx.html` 时侧栏对应文章加 `current` class（背景色 + 强调色）
（3）去掉黑白切换动画：body 删除 `transition: background 0.3s, color 0.3s`，深浅色切换不再闪光
（4）关于页/正文禁止圆点编号：所有 `- ` 列表批量改 `（1）（2）` 论文格式（GB/T 7713 规范）
（5）冷热源独立公式修复：12 处多行 `$$\n...\n$$` 全部改为单行 `$$...$$`
（6）表格删除线修复：hvac-load.md 表格内半角 `~` 改为全角 `～`（U+FF5E）
（7）加粗紧邻公式修复：layout.ejs 新增 `fixUnmatchedBold` 客户端 JS 兜底，处理 `**xx$yy$**` 模式

### 2026-06-05 · v4.4.2 视觉与体验优化
（1）深色模式链接用 `#F0B894`（高对比），去 `color transition` 防跳转闪烁
（2）悬浮按钮：浅色模式图标文字=黑，深色模式=白（高对比）
（3）公式加横向滚动（手机不再丢失长公式）+ 强制 Tinos 字体
（4）块级公式 `$$...$$` 客户端手动渲染（marked 不识别时兜底）
（5）`**` 与公式紧邻修复（`</strong>$` 之间插入零宽空格）
（6）首页/侧边栏 KB 名称改为"建筑自控总览 / 冷热源总览 / 暖通空调总览"
（7）删除 11 篇文章中的 mermaid 和 ASCII 字符图，改纯文字

### 2026-06-05 · v4.4.1 暖通 KB 路径修正
（1）修复 404：暖通空调 KB 路径从子目录式 `/kb/hvac/xxx.html` 改为扁平式 `/kb/hvac-xxx.html`，与项目统一风格一致
（2）去除应试教辅口吻：所有暖通文章删除"核心考点表/考试占比/公式速记"等内容
（3）改为工程原理导向，保留概念、公式、计算过程，添加"设计含义"段落

### 2026-06-05 · v4.4 暖通空调 KB
（1）**新增「暖通空调」知识库**（id: hvac）：6 章覆盖考试四大题型（分析/画图/水力/设计）
（2）第一章 绪论：HVAC 任务、室内外设计参数、不保证率 0.4%
（3）第二章 热湿负荷：得热 ≠ 负荷（辐射延迟）、冷负荷系数、房间/系统负荷、热湿比
（4）第三章 供暖系统：散热器放热系数、热水供暖、高层分区、**不等温降水力计算**
（5）第四章 空气调节：**质调节焓湿图**、**二次回风**、**变风量节能**、FCU+新风、VRV 配比、热回收 VRV
（6）第五章 气流组织：典型模式、ADPI 评价
（7）第六章 防排烟（**新增**）：烟气特性、机械排烟量、加压送风余压
（8）全部用 mermaid 画系统图与流程图

### 2026-06-05 · v4.3 KaTeX 本地化
（1）KaTeX 数学库从 jsdelivr CDN 改为本地托管，3 个文件放入 `themes/cimu-kb/source/lib/katex/`
（2）全文零公共 CDN：所有 JS/CSS 走阿里云 ESA Pages 加速，避免 jsdelivr 抽风影响访问
（3）关于页列表去除项目编号圆点（`list-style: none`）
（4）修复 `refrigeration-equipment.md` 中 `\dot{V}_{actual}` 渲染异常，改用 `\dot V_{\text{actual}}`
（5）修复 `refrigeration-cycle.md` 轴功率公式，补充 `\text{}` 包裹下标
（6）`project_rules.md` 新增"禁止公共 CDN"硬规则

### 2026-06-04 · v4.2 关于页同步
（1）关于页全面更新：补充 v3.0~v4.1 完整更新记录（原停留在 v2.8）
（2）关于页新增"开源项目"章节（GitHub 仓库、CC BY-SA 4.0 许可、致谢）
（3）调整关于页样式与 KB 文章保持一致（h2/h3 间距、列表缩进、链接下划线）
（4）在 `project_rules.md` 增加"任何更新必须同步更新关于页"规则
（5）修复 `style.css` 关于页 list 样式缺失的问题

### 2026-06-04 · v4.1 扩展性架构
（1）新增 `extensibility.md`、`comment_workflow.md`、`media_workflow.md` 三大工作流规则
（2）完善 README 目录结构与修改联动规则
（3）为未来扩展（焓湿图/曲线/视频/PDF/评论）提供标准化模板

### 2026-06-04 · v4.0 仓库重置
（1）仓库完全重置，删除 58 个旧提交，2 个干净提交重建历史
（2）创建 `meta_rules.md` 通用元规则
（3）清理所有无关文件（RSS、GitHub Actions、旧主题配置、retrospectives）
（4）撤销旧 GitHub Token，修复 SETUP_GUIDE.md 中的隐私信息

### 2026-06-04 · v3.1 KaTeX 公式
（1）集成 KaTeX 数学公式渲染库
（2）修复冷热源知识库所有公式显示问题

### 2026-06-04 · v3.0 规则体系
（1）添加项目规则「更新完成检查清单」与同步流程

### 2026-06-04 · v2.9 页脚修复
（1）修复页脚宽度问题，页脚独立横跨整个正文区域

### 2026-06-02 · v2.8 页脚撑满
（1）KB 页脚撑满正文列底部；关于页去除编号圆点

### 2026-06-02 · v2.7 页脚缩入
（1）KB 文章页脚移入 `.kb-main` 内部

### 2026-06-02 · v2.6 视口填满
（1）侧边栏/TOC 改 height 填至视口底部

### 2026-06-02 · v2.5 路径修复
（1）面包屑路径精确匹配；首页分栏响应式 3→2→1 列

### 2026-06-02 · v2.4 Sticky 修复
（1）侧边栏/TOC sticky 改 max-height；折叠树全展开；添加 favicon

### 2026-06-02 · v2.3 侧边栏修复
（1）面包屑精确匹配；侧边栏自动切换 KB；首页分栏卡片布局

### 2026-06-02 · v2.2 导航固定
（1）取消滚动动画；命名精简→建筑自控/冷热源

### 2026-06-02 · v2.1 色调升级
（1）全站陶土色调升级；KB 路径 /kb/ + /kb/hc/ 直连总览页

### 2026-06-02 · v2.0 全面重构
（1）知识库 YAML 数据驱动；Open Graph + JSON-LD SEO

### 2026-05-26
（1）修复移动端响应式 bugs；全局去商业词

### 2026-05-25
（1）五大知识库模块初版；水力计算工具上线

### 2026-05-24
（1）项目启动
