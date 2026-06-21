---
created: 2026/06/21 16:25
updated: 2026/06/21 12:30
---
# 根据 frontmatter 显示更新时间

实现在 Markdown 顶部用 YAML frontmatter 声明创建时间与更新时间，页面会在标题下方自动展示，而不是把 `---` 块原样渲染出来。

## 用法

在 `.md` 文件最顶部添加 frontmatter：

```md
---
created: 2026/06/21 11:23
updated: 2026/06/21 12:15
---
# 文章标题

正文...
```

展示效果（标题下方一行灰色小字）：

> 创建于 2026/06/21 11:23 · 更新于 2026/06/21 12:15

说明：

- `created`、`updated` 可以只写其中一个，页面只会显示已有的字段
- 没有 frontmatter 的页面不受影响（例如首页 `README.md`）
- 日期格式目前自由填写，建议统一为 `YYYY/MM/DD HH:mm`

## 为什么不用 `{docsify-updated}`

Docsify 内置的 `{docsify-updated}` 读取的是 HTTP 响应头里的 `Last-Modified`。在 GitHub Pages 等静态部署场景下，这个时间往往是**整站最后一次部署的时间**，各篇笔记会显示相同，不适合作为单篇文章的更新时间，且容易被外部影响丢失或错误。而作为内容数据的 frontmatter，更适合作为单篇文章的更新时间，可以手动或自动维护，不被外部影响，更可控。

## 实现方式

逻辑都在 `docs/index.html` 中，依赖两部分：

### 1. front-matter 插件（官方）

解析 Markdown 顶部的 `---` 块，去掉原始 YAML 显示，数据存入 `vm.frontmatter`。

```html
<script src="//cdn.jsdelivr.net/npm/docsify@4/lib/plugins/front-matter.min.js"></script>
```

### 2. docMeta 自定义插件

在 `afterEach` 钩子里读取 `vm.frontmatter.created` / `vm.frontmatter.updated`，插入到第一个 `<h1>` 标题下方：

```js
plugins: [
  function docMeta(hook, vm) {
    hook.afterEach(function (html, next) {
      var fm = vm.frontmatter || {};
      var created = fm.created;
      var updated = fm.updated;

      if (!created && !updated) {
        next(html);
        return;
      }

      var parts = [];
      if (created) {
        parts.push('创建于 <time datetime="' + created + '">' + created + '</time>');
      }
      if (updated) {
        parts.push('更新于 <time datetime="' + updated + '">' + updated + '</time>');
      }

      var meta = '<div class="doc-meta">' + parts.join('<span aria-hidden="true"> · </span>') + '</div>';
      var updatedHtml = html.replace(/(<h1[^>]*>[\s\S]*?<\/h1>)/i, '$1' + meta);

      next(updatedHtml);
    });
  },
],
```

样式定义在同文件的 `<style>` 中，类名为 `.doc-meta`。
