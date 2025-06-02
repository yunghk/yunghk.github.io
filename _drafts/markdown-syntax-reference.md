
---
title: "Markdown Syntax Reference"
layout: page
---

# Markdown Syntax Reference

A concise reference for standard Markdown elements.

---

## Headings

```markdown
# H1
## H2
### H3
#### H4
##### H5
###### H6
```

## Emphasis

```markdown
*italic* or _italic_
**bold** or __bold__
~~strikethrough~~
```

## Lists

### Unordered

```markdown
- Item 1
- Item 2
  - Nested Item
```

### Ordered

```markdown
1. First item
2. Second item
   1. Sub-item
```

## Links

```markdown
[Link Text](https://example.com)
```

## Images

```markdown
![Alt Text](https://example.com/image.jpg)
```

## Blockquote

```markdown
> This is a quote.
```

## Code

### Inline

```markdown
Use the `code` tag.
```

### Block

<pre>
```language
function test() {
  return true;
}
```
</pre>

## Horizontal Rule

```markdown
---
```

## Tables

```markdown
| Header 1 | Header 2 |
|----------|----------|
| Row 1    | Cell     |
| Row 2    | Cell     |
```

## Task Lists

```markdown
- [ ] Incomplete
- [x] Complete
```

## Footnotes

```markdown
This is a sentence with a footnote.[^1]

[^1]: Footnote text.
```

## Escaping Characters

```markdown
\*literal asterisks\*
```

---

_Last updated: {{ site.time | date: "%Y-%m-%d" }}_
