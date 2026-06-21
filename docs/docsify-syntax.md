---
created: 2026/06/21 16:45
updated: 2026/06/21 17:30
---
# Docsify 语法与转义

本站 Docsify v5（`5.0.0-rc.4`），core 主题。

## 扩展语法

| 语法 | 写法 |
|------|------|
| 提示框 | `> [!NOTE]`、`> [!TIP]`；旧版 `!>`、`?>` |
| 链接 / 图片属性 | `[text](/path ':target=_blank')`、`![](a.png ':size=100')` |
| frontmatter | 顶部 `---` 块，需 front-matter 插件 |
| 更新时间占位符 | {docsify-updated<span>}</span>（读 HTTP Last-Modified） |

## {docsify-updated<span>}</span> 转义

Markdown 解析前，全文 {docsify-updated<span>}</span> 会被替换成文件修改时间。反引号、代码块**挡不住**。

**文档里要展示变量名本身**，在 `}` 前加 `<span>`，打断匹配正则，且渲染时span不显示：

```md
{docsify-updated<span>}</span>
```

**实际使用**（插件、页脚）写完整占位符，可配 `formatUpdated: '{YYYY}/{MM}/{DD} {HH:mm}'`。

展示其他特殊语法（callout、`:emoji:`、链接属性等）→ 放 fenced 代码块。
