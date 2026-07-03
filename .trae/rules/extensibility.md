# 扩展性架构规则（资源与组件）

> **本文件目标**：让后期增加焓湿图动画、可调节曲线、PDF、视频、评论等功能时，**管理成本最低**。

---

## 〇、设计哲学

### 三个核心原则

1. **数据驱动**（不硬编码）
   - 资源清单、组件参数、媒体文件全部用 YAML/JSON 声明
   - 新增内容只改数据，不改模板

2. **按需加载**（不预加载）
   - JS/CSS 懒加载到 `.trae/rules/assets/` 或 `themes/cimu-kb/source/js/components/`
   - 每个功能独立成模块，按需注册到 layout

3. **可插拔**（不耦合）
   - 每个新功能是一个独立模块，禁用/删除一个不影响其他
   - 通过统一的"组件注册表"接入系统

---

## 一、媒体资源管理

### 1.1 资源存储规范

```
source/
├── assets/                          # ★ 所有静态资源唯一目录
│   ├── images/                      # 图片（jpg/png/webp/svg）
│   │   ├── kb/                      # 知识库配图
│   │   │   ├── refrigeration-cycle/ # 按文章分子目录
│   │   │   └── ...
│   │   ├── tools/                   # 工具配图
│   │   └── common/                  # 通用图片（logo/icon）
│   ├── videos/                      # 视频（mp4/webm）
│   │   └── kb/
│   ├── pdfs/                        # PDF 文档
│   │   └── kb/
│   └── interactive/                 # 交互式资源
│       ├── psychrometric/           # 焓湿图组件
│       ├── curves/                  # 性能曲线组件
│       └── charts/                  # 数据图表组件
```

**严禁**：
- ❌ 图片/视频放在 `themes/cimu-kb/source/`（主题源）
- ❌ 资源散落在各 `.md` 同级目录
- ❌ 中文路径（PowerShell 操作易出问题）

### 1.2 资源引用规范

在 Markdown 中引用资源：

```markdown
<!-- 图片 -->
![制冷循环示意图](/assets/images/kb/refrigeration-cycle/overview.png)

<!-- PDF 下载 -->
[下载完整手册](/assets/pdfs/kb/refrigeration-manual.pdf)

<!-- 视频 -->
<video src="/assets/videos/kb/boiler-operation.mp4" controls></video>

<!-- 交互式组件（详见第二章） -->
[psychrometric:id="standard-atmospheric"]
```

**自动优化**：
- `image_viewer.ejs` 自动加 lazy loading
- 视频自动加 `preload="metadata"`
- PDF 自动加下载按钮 + 文件大小提示

### 1.3 资源元数据（可选）

对于关键资源，可选创建 `source/_data/assets.yml`：

```yaml
images:
  - path: /assets/images/kb/refrigeration-cycle/overview.png
    title: 制冷循环示意图
    description: 标准蒸气压缩制冷循环的四个过程
    author: 此木的车间
    license: CC-BY-SA-4.0
    tags: [refrigeration, diagram]

videos:
  - path: /assets/videos/kb/boiler-operation.mp4
    title: 锅炉运行演示
    duration: 180
    thumbnail: /assets/images/kb/boiler-thumb.jpg
```

**自动好处**：未来可基于此生成资源索引页、SEO 优化、自动图注。

---

## 二、可交互组件管理

### 2.1 组件目录结构

```
themes/cimu-kb/source/js/components/
├── registry.js          # 组件注册表（核心）
├── psychrometric/       # 焓湿图组件
│   ├── index.js
│   ├── styles.css
│   └── README.md
├── curves/              # 性能曲线组件
│   ├── index.js
│   ├── styles.css
│   └── README.md
├── image-viewer/        # 图片查看器
│   ├── index.js
│   └── styles.css
├── pdf-viewer/          # PDF 阅读器
│   ├── index.js
│   └── styles.css
├── video-player/        # 视频播放器
│   ├── index.js
│   └── styles.css
├── comment/             # 评论组件
│   ├── index.js
│   └── styles.css
└── _template/           # 新组件模板（开发时复制）
    ├── index.js
    ├── styles.css
    └── README.md
```

### 2.2 组件接口标准

每个组件必须实现统一接口：

```javascript
// themes/cimu-kb/source/js/components/_template/index.js
export default {
  // 必填：组件唯一标识
  name: 'component-name',
  
  // 必填：组件版本
  version: '1.0.0',
  
  // 必填：组件依赖
  dependencies: [],  // 例如 ['katex', 'd3']
  
  // 必填：组件初始化入口
  init(container, options) {
    // container: DOM 元素
    // options: 组件参数
  },
  
  // 选填：销毁方法
  destroy() {
    // 清理事件监听、移除 DOM
  },
  
  // 选填：参数校验
  validate(options) {
    return true;
  }
};
```

### 2.3 组件注册表

**所有组件在 [registry.js](file:///c:/Users/King/Desktop/260528hvac-hexo-trea/themes/cimu-kb/source/js/components/registry.js) 中统一注册**：

```javascript
// registry.js
const components = {
  'psychrometric': () => import('./psychrometric/index.js'),
  'curves': () => import('./curves/index.js'),
  'image-viewer': () => import('./image-viewer/index.js'),
  'pdf-viewer': () => import('./pdf-viewer/index.js'),
  'video-player': () => import('./video-player/index.js'),
  'comment': () => import('./comment/index.js'),
  // 新增组件只需在这里加一行
};

export async function mountComponent(name, container, options) {
  if (!components[name]) {
    console.warn(`Component not found: ${name}`);
    return;
  }
  
  const module = await components[name]();
  module.default.init(container, options);
}
```

**好处**：
- 新增组件**只改一行**（`registry.js`）
- 删除组件**只删一行** + 组件目录
- 禁用组件**注释一行**

### 2.4 Markdown 调用语法

**简洁的 inline 语法**（AI 自动转换）：

```markdown
<!-- 基础组件 -->
[component:psychrometric id="standard"]

<!-- 带参数 -->
[component:curves type="chiller-performance" data="cooling"]

<!-- 完整语法（带参数和说明） -->
[component:image-viewer src="/assets/.../x.png" caption="图1" zoom="true"]
```

**AI 转换规则**（在 layout.ejs 中）：

```html
<component-tag data-component="psychrometric" data-id="standard"></component-tag>
```

然后由 `component-loader.js` 在页面加载时扫描所有 `<component-tag>` 并自动挂载。

---

## 三、新增功能的工作流（核心）

### 3.1 新增组件的标准流程

```
1. 复制 _template/ 目录为新组件名
2. 实现 index.js（遵守 2.2 接口）
3. （可选）实现 styles.css
4. 在 registry.js 中注册
5. 在 layout.ejs 引入（如果需要全局 CSS）
6. 在 Markdown 中用 [component:name ...] 引用
7. 提交 → 推送 → ESA Pages 自动部署
```

**总成本**：5-15 分钟，**只动 3-4 个文件**。

### 3.2 新增内容类型的标准流程

| 需求 | 改动文件 | 总成本 |
|------|---------|--------|
| 新增一篇文章 | `source/_data/kb-articles.yml` + `source/kb/xxx.md` | 5 分钟 |
| 新增图片 | 复制到 `source/assets/images/...` + Markdown 引用 | 2 分钟 |
| 新增 PDF | 复制到 `source/assets/pdfs/...` + Markdown 引用 | 2 分钟 |
| 新增视频 | 复制到 `source/assets/videos/...` + Markdown `<video>` 标签 | 3 分钟 |
| 新增交互组件 | 复制 `_template/` + 改 `registry.js` + Markdown 引用 | 10-30 分钟 |
| 新增评论 | 已有 `comment` 组件 + 配置 ESA 函数 | 1 小时 |

### 3.3 升级/降级组件

```
1. 修改 components/<name>/index.js
2. 如有破坏性变更，更新版本号
3. 更新使用方 Markdown（如有需要）
4. 提交推送
```

**热重载**：开发时改 `.js` 刷新即生效（hexo server）。

---

## 四、第三方资源加载

### 4.1 CDN 资源统一管理

所有外部 CDN 资源在 [layout.ejs](file:///c:/Users/King/Desktop/260528hvac-hexo-trea/themes/cimu-kb/layout/layout.ejs) 的 `<head>` 中按**按需**加载：

```html
<!-- 基础 -->
<link rel="stylesheet" href="/css/style.css">

<!-- KaTeX（数学公式） -->
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/katex@0.16.9/dist/katex.min.css">
<script defer src="https://cdn.jsdelivr.net/npm/katex@0.16.9/dist/katex.min.js"></script>

<!-- 新增第三方库时，遵循：
     1. 优先用 jsdelivr/unpkg
     2. 加 defer 属性
     3. 加 SRI hash（重要！防止 CDN 被劫持）
     4. 在 PROJECT_RULES 中登记
-->
```

### 4.2 SRI（Subresource Integrity）

**重要**：所有外部 CDN 资源必须加 SRI：

```html
<script defer 
        src="https://cdn.jsdelivr.net/npm/katex@0.16.9/dist/katex.min.js"
        integrity="sha384-..."
        crossorigin="anonymous"></script>
```

工具：https://www.srihash.org/

---

## 五、未来扩展路线图（参考）

### Phase 1：基础设施（已完成）
- ✅ 静态站点
- ✅ 知识库文章
- ✅ 搜索
- ✅ KaTeX 公式

### Phase 2：多媒体（建议实现顺序）
1. **图片查看器**（lightbox 风格，2-3 小时）
2. **视频播放器**（基于 video.js 或原生，3-4 小时）
3. **PDF 阅读器**（pdf.js，4-6 小时）

### Phase 3：交互组件（重头戏）
1. **焓湿图组件**（d3.js + canvas，1-2 天）
2. **性能曲线组件**（chart.js，1 天）
3. **控制回路动画**（SVG + JS，2-3 天）

### Phase 4：评论系统
1. **ESA 函数后端**（写评论 API，0.5 天）
2. **前端评论组件**（giscus/自制，1 天）
3. **评论管理**（GitHub Issues 模式，0.5 天）

### Phase 5：高级功能
1. 用户登录（GitHub OAuth，1 天）
2. 收藏/点赞（localStorage + ESA KV，0.5 天）
3. 文章版本控制（基于 git 历史，1 天）

---

## 六、命名规范

| 类型 | 命名 | 示例 |
|------|------|------|
| 组件名 | 小写连字符 | `psychrometric`, `image-viewer` |
| 资源文件 | 小写连字符 | `refrigeration-cycle-overview.png` |
| 资源目录 | 小写连字符 | `refrigeration-cycle/` |
| 资源路径 | 绝对路径（带 /） | `/assets/images/kb/.../x.png` |
| 组件参数 | 小驼峰 | `showGrid`, `dataSource` |

**严禁**：
- ❌ 中文路径
- ❌ 空格
- ❌ 特殊字符（除 `-` 和 `_`）

---

## 七、性能约束

### 7.1 资源体积限制

| 类型 | 单文件上限 | 总计（单页） |
|------|----------|------------|
| 图片 | 500KB | 2MB |
| 视频 | 10MB | 20MB |
| PDF | 20MB | 30MB |
| JS（组件） | 100KB | 300KB |
| CSS（组件） | 50KB | 150KB |

**超过限制**：
- 图片：转 webp + lazy loading
- 视频：转 webm + 分段
- 大文件：考虑外部托管（OSS）

### 7.2 加载策略

| 资源 | 加载时机 |
|------|---------|
| 关键 CSS | `<head>` 内联或同步 |
| 关键 JS | `<body>` 末尾 + defer |
| 组件 JS | 滚动到视口时（IntersectionObserver） |
| 图片 | lazy + async decode |
| 视频/大文件 | 点击时加载 |
| 评论组件 | 滚动到底部时加载 |

---

## 八、调试与监控

### 8.1 开发模式

```javascript
// 在 layout.ejs 顶部检测
if (location.hostname === 'localhost') {
  window.__DEV__ = true;
}
```

### 8.2 错误处理

每个组件必须有：
- `try/catch` 包裹初始化
- 控制台友好错误信息
- 降级方案（如 JS 失败时显示静态占位）

### 8.3 性能监控（可选）

```javascript
// 仅在生产环境
if (!window.__DEV__ && 'performance' in window) {
  window.addEventListener('load', () => {
    const perf = performance.getEntriesByType('navigation')[0];
    console.log('Page load:', perf.loadEventEnd, 'ms');
  });
}
```

---

## 九、迁移与升级

### 9.1 升级 Hexo 版本

```bash
# 1. 备份
cp package.json package.json.bak

# 2. 升级
npm install hexo@latest

# 3. 测试
npm run server

# 4. 验证所有插件
npm ls --depth=0
```

### 9.2 升级主题

主题 `cimu-kb` 是自研的，升级方式：
- **小改**：直接修改 + 提交
- **大改**：先备份到 `themes/cimu-kb-v{old}/`，新主题用 `themes/cimu-kb-v{new}/`

### 9.3 升级组件

每个组件独立版本管理，遵循 SemVer：
- **MAJOR**：破坏性 API 变更
- **MINOR**：新增功能（向后兼容）
- **PATCH**：bug 修复

---

## 十、扩展性检查清单

**每次新增强功能前，问自己**：

- [ ] 是否能用现有 `registry.js` 接口接入？
- [ ] 资源是否放在 `source/assets/` 正确子目录？
- [ ] 是否提供了组件参数校验？
- [ ] 是否处理了加载失败/禁用 JS 的降级？
- [ ] 是否影响其他页面的加载性能？
- [ ] 是否在 `SETUP_GUIDE.md` 中补充使用说明？

**通过所有检查** → 提交推送；**有未通过** → 先重构再提交。
