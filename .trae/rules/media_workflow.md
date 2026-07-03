# 多媒体资源工作流

> **本文件目标**：图片、视频、PDF、SVG 等多媒体资源的统一管理规范。

---

## 〇、为什么需要规则

多媒体资源是项目体积膨胀的主要原因。规则化是为了：
- 统一资源路径和命名
- 优化加载性能
- 避免大文件占用部署空间
- 沉淀"什么时候用什么方案"的经验

---

## 一、资源类型与格式选择

### 1.1 决策树

```
需要展示的内容是什么？
├── 静态示意图/插图 → SVG（矢量） > WebP（位图）
├── 照片/写实图 → WebP > JPG
├── 透明背景图标 → SVG > PNG
├── 动画/演示 → GIF（短） / 视频（长）
├── 复杂文档 → PDF
└── 交互式图表 → 交互组件（详见 extensibility.md）
```

### 1.2 推荐格式

| 类型 | 一选 | 二选 | 不推荐 |
|------|------|------|--------|
| 静态图 | WebP | PNG/SVG | JPG/BMP |
| 照片 | WebP | JPG（高质量） | PNG |
| 图标 | SVG | 字体图标 | 位图 |
| 动画 | MP4（短用 GIF） | WebM | 大量 GIF |
| 文档 | PDF | - | DOC |
| 数据可视化 | 交互组件 | SVG | 位图截图 |

### 1.3 格式转换工具

```bash
# PNG → WebP
cwebp -q 80 input.png -o output.webp

# JPG → WebP
cwebp -q 80 input.jpg -o output.webp

# 视频压缩
ffmpeg -i input.mp4 -c:v libx264 -crf 23 -c:a aac -b:a 128k output.mp4
```

---

## 二、资源目录与命名

### 2.1 目录结构

```
source/assets/
├── images/
│   ├── kb/
│   │   ├── refrigeration-cycle/
│   │   │   ├── overview.webp         # 概述图
│   │   │   ├── process-1-2.webp      # 过程 1-2
│   │   │   └── diagram.svg           # 矢量图
│   │   ├── boilers/
│   │   └── ...
│   ├── tools/
│   │   └── hydraulic-calc/
│   └── common/
│       ├── logo.svg
│       └── icons/
├── videos/
│   └── kb/
│       └── boiler-operation.mp4
├── pdfs/
│   └── kb/
│       └── refrigeration-manual.pdf
└── interactive/
    └── (详见 extensibility.md)
```

### 2.2 命名规范

**格式**：`<类别>-<描述>-<版本>.<格式>`

| 规范 | ❌ 错误 | ✅ 正确 |
|------|--------|--------|
| 全小写 | `Overview.PNG` | `overview.png` |
| 连字符分隔 | `refrigeration cycle.webp` | `refrigeration-cycle.webp` |
| 描述性 | `img1.webp` | `refrigeration-overview.webp` |
| 避免日期 | `20260604_image.webp` | `refrigeration-overview-v2.webp` |
| 单数主语 | `pumps_and_valves.webp` | `pump-valve.webp` |

### 2.3 版本管理

**资源变更时**：
- **小改**（色彩、文字）：覆盖
- **大改**（结构变化）：加 `-v2` 后缀
- **彻底重做**：移到 `_archive/` 目录

---

## 三、Markdown 引用规范

### 3.1 图片

```markdown
<!-- 基础 -->
![alt 文字](/assets/images/kb/.../image.webp)

<!-- 带标题 -->
![制冷循环概述](/assets/images/kb/refrigeration-cycle/overview.webp "图 1: 标准蒸气压缩制冷循环")

<!-- 引用 SVG 矢量图 -->
![电路图](/assets/images/kb/control-loop/circuit.svg)

<!-- 自动 lightbox（如果启用 image-viewer 组件） -->
![示意图](/assets/images/kb/.../x.webp){.lightbox}
```

### 3.2 视频

```markdown
<!-- 基础 -->
<video src="/assets/videos/kb/boiler.mp4" controls></video>

<!-- 带封面 -->
<video 
  src="/assets/videos/kb/boiler.mp4" 
  poster="/assets/images/kb/boilers/cover.webp"
  controls
  preload="metadata">
</video>

<!-- 自动播放（慎用） -->
<video 
  src="..." 
  autoplay 
  muted 
  loop 
  playsinline>
</video>
```

### 3.3 PDF

```markdown
<!-- 简单下载链接 -->
[下载手册](/assets/pdfs/kb/manual.pdf)

<!-- 带图标和大小 -->
[📕 完整手册 (PDF, 2.3MB)](/assets/pdfs/kb/manual.pdf)

<!-- 嵌入预览（需要 pdf-viewer 组件） -->
[component:pdf-viewer src="/assets/pdfs/kb/manual.pdf"]
```

### 3.4 SVG 矢量图

```markdown
<!-- 引用 -->
![示意](/assets/images/kb/.../diagram.svg)

<!-- 直接嵌入（适合小图标） -->
<svg>...</svg>
```

---

## 四、性能优化

### 4.1 图片优化

**必做**：
- ✅ 转换 WebP（质量 75-85）
- ✅ 调整到显示尺寸的 2 倍（Retina）
- ✅ 添加 `width`/`height`（防 layout shift）

**选做**：
- 生成多尺寸 srcset
- 启用 lazy loading
- 关键图加 fetchpriority="high"

### 4.2 视频优化

**必做**：
- ✅ H.264 编码（兼容性最好）
- ✅ 压缩到合理码率（CRF 23-28）
- ✅ 加 `preload="metadata"`（不自动下载）

**选做**：
- 同时提供 WebM（更小）
- 分段加载（HLS/DASH）
- 视频封面图

### 4.3 PDF 优化

**必做**：
- ✅ 压缩图片（PDF 内的图片）
- ✅ 优化字体子集
- ✅ 移除元数据（隐私）

**工具**：
```bash
# 压缩 PDF
gs -sDEVICE=pdfwrite -dCompatibilityLevel=1.4 -dPDFSETTINGS=/ebook -dNOPAUSE -dQUIET -dBATCH -sOutputFile=output.pdf input.pdf
```

### 4.4 资源体积监控

```bash
# 找出最大的资源
find source/assets -type f -exec du -h {} + | sort -rh | head -20
```

**超过限制立即处理**（详见 extensibility.md 第七章）。

---

## 五、CDN 与加速

### 5.1 当前方案

**ESA Pages 直接托管**：
- ✅ 简单：推到 GitHub 自动部署
- ❌ 限制：阿里云 ESA 单文件 50MB，单仓库 1GB

### 5.2 大文件方案

**视频 / 大 PDF（>10MB）**：

| 方案 | 优点 | 缺点 |
|------|------|------|
| 阿里云 OSS | 便宜、CDN 加速 | 额外配置 |
| 阿里云 ESA 资源 | 集成度高 | 容量限制 |
| 外部服务（YouTube/B站） | 零成本 | 依赖第三方 |
| Cloudflare R2 | 出口免费 | 配置稍复杂 |

**推荐**：
- **视频 > 10MB** → 阿里云 OSS 或外部视频平台
- **PDF > 20MB** → 阿里云 OSS
- **图片** → 始终直接托管

### 5.3 OSS 集成

如果用阿里云 OSS：

```markdown
<!-- Markdown 中 -->
![图片](https://cdn.cimugarage.cn/.../x.webp)

<!-- 或者在 _config.yml 中配置 baseurl -->
cdn_base: https://cdn.cimugarage.cn

<!-- 组件中 -->
const url = `${config.cdn_base}${path}`;
```

---

## 六、可访问性（A11Y）

### 6.1 图片

- ✅ **必填 `alt`**：描述图片内容
- ✅ 装饰性图片用 `alt=""`
- ❌ 不要在 alt 中写"图片"或"照片"
- ✅ 复杂图加 `figcaption` 或 `title`

### 6.2 视频

- ✅ 加 `<track>` 字幕（如果有人说话）
- ✅ 加 `aria-label`
- ✅ 自动播放**必须**静音

### 6.3 PDF

- ✅ 提供 HTML 替代（如果可能）
- ✅ PDF/A 格式（长期可读）
- ✅ 文字版可复制

---

## 七、SEO 优化

### 7.1 图片 SEO

```markdown
<!-- 文件名含关键词 -->
![制冷循环四大过程](/assets/images/kb/refrigeration-cycle/four-processes.webp)

<!-- alt 描述完整 -->
![蒸发-冷凝-压缩-节流四大过程的温度-焓图](/assets/images/.../ph-diagram.webp)
```

### 7.2 结构化数据

为重要图片添加 JSON-LD：

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "ImageObject",
  "contentUrl": "/assets/.../x.webp",
  "name": "制冷循环示意图",
  "description": "..."
}
</script>
```

### 7.3 Sitemap

为媒体资源生成 sitemap（在 _config.yml 中）：
```yaml
sitemap:
  path: sitemap.xml
  rel: false
  tags: true
  categories: true
```

---

## 八、版权与许可

### 8.1 自有资源

- 项目作者：此木的车间
- 默认许可：CC-BY-SA-4.0（可商用需署名）
- 商业使用：联系作者

### 8.2 第三方资源

- 引用前**必须**确认版权
- 标注来源和许可
- 优先选择 CC0/Public Domain
- 避免 GPL（传染性）

### 8.3 资源登记

在 `source/_data/assets.yml` 中登记所有非自有资源：

```yaml
third_party:
  - path: /assets/images/.../x.webp
    source: https://commons.wikimedia.org/wiki/File:Example.png
    author: John Doe
    license: CC-BY-SA-4.0
    url: https://creativecommons.org/licenses/by-sa/4.0/
```

---

## 九、迁移与清理

### 9.1 定期清理

每季度检查一次：

```bash
# 找出未被引用的资源
grep -rh "assets/" source/ | grep -oE '/assets/[^)" ]+' | sort -u > used.txt
find source/assets -type f | sed 's|source||' | sort -u > all.txt
comm -23 all.txt used.txt  # 未引用的资源
```

### 9.2 备份策略

- **Git 仓库**：所有 < 50MB 的资源
- **OSS / 外部存储**：视频和大文件
- **本地备份**：每季度一份完整快照

### 9.3 资源删除流程

1. 确认无引用
2. `git rm` 资源
3. 提交推送
4. ESA Pages 自动重新部署（移除资源）
5. 通知（如有外部链接）

---

## 十、检查清单

**添加新资源前**：

- [ ] 文件名是否符合命名规范？
- [ ] 是否用最佳格式（WebP/MP4）？
- [ ] 是否在 `source/assets/` 正确子目录？
- [ ] 大小是否在限制内（图片 500KB，视频 10MB）？
- [ ] Markdown 引用是否带 alt 文本？
- [ ] 是否登记了第三方资源（如适用）？

**每季度检查**：

- [ ] 是否有未引用资源？
- [ ] 是否有大文件需要迁移到 OSS？
- [ ] 是否有重复或相似资源？
- [ ] 备份是否最新？
