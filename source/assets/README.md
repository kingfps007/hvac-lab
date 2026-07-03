# 静态资源目录

> 本目录是项目**所有静态资源的唯一根目录**（图片、视频、PDF、交互式资源）。
> 详细规范见 [`.trae/rules/media_workflow.md`](../../.trae/rules/media_workflow.md)。

## 目录结构

```
source/assets/
├── images/          # 图片（jpg/png/webp/svg）
│   ├── kb/          # 知识库配图（按文章分子目录）
│   ├── tools/       # 工具配图
│   └── common/      # 通用图片（logo/icon）
├── videos/          # 视频（mp4/webm）
│   └── kb/          # 知识库视频
├── pdfs/            # PDF 文档
│   └── kb/          # 知识库 PDF
└── interactive/     # 交互式资源
    ├── psychrometric/   # 焓湿图组件
    ├── curves/          # 性能曲线组件
    └── charts/          # 数据图表组件
```

## 关键规则（速查）

- **路径**：绝对路径（带前导 `/`），如 `/assets/images/kb/.../x.webp`
- **命名**：`<类别>-<描述>-<版本>.<格式>`，全小写连字符
- **格式**：图 WebP / 视频 MP4 / 文档 PDF（详见 media_workflow.md 第一章）
- **引用**：Markdown 中 `![alt](/assets/...)`；组件中 `<img src="/assets/...">`
- **禁止**：❌ 中文路径、❌ 散落到 `themes/` 下、❌ 半角空格

## 子目录创建

子目录按需创建（首次添加资源时）。无需预先建空目录 + .gitkeep。
