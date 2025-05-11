---
title: "My First Post"
date: "2025-04-17"
description: "This is a short description of the post."
previewImage: "cover.png"
---

# Heading Levels

# H1 – Largest
## H2
### H3
#### H4
##### H5
###### H6 – Smallest

---

## Text Styling

This is a paragraph with:

- *Italic*
- **Bold**
- ***Bold & Italic***
- ~~Strikethrough~~
- `Inline code span`

And a custom HTML tag for testing raw HTML passthrough:

<p style="color:teal;">This is a teal paragraph via HTML.</p>

---

## Blockquotes

> A simple blockquote
>> A nested blockquote

---

## Lists

### Unordered
- Item A
    - Subitem A.1
    - Subitem A.2
- Item B
- Item C

### Ordered
1. First
2. Second
    1. Second.A
    2. Second.B
3. Third

### Task List
- [x] Write sample file
- [ ] Implement parser
- [ ] Test edge cases

---

## Links & Images

[OpenAI Website](https://openai.com "Go to OpenAI")  
![Placeholder Kitten](https://raw.githubusercontent.com/Murasaki-Labs/blog_articles_static/refs/heads/main/articles/my-first-post/cover.png "A cute kitten")

---

## Tables

| Feature       | Syntax Example        |
|---------------|-----------------------|
| Header Row    | `| Col1 \| Col2 |`     |
| Alignment     | `:---`, `:---:`, `---:`|
| Bold in Cell  | `**bold**`            |

| Left Align | Center Align | Right Align |
|:-----------|:------------:|------------:|
| col1       | col2         | col3        |
| data       | more data    | even more   |

---

## Code Blocks

```go
// Go example
package main

import "fmt"

func main() {
fmt.Println("Hello, Markdown → HTML!")
}
```

```python
# Python example
def greet(name):
print(f"Hello, {name}!")
```

---

## Footnotes

Here's a sentence with a footnote.[^note]

[^note]: And this is the footnote text.

---

## Definition List

Term 1
: Definition 1

Term 2
: Definition 2

---

## Horizontal Rules

---  
***  
___  

---

## Emojis & Entities

:+1: :rocket: &copy; 2025

---

## Collapsible Section (HTML)

<details>
<summary>Click to expand!</summary>

You can put **Markdown** _inside_ this HTML block.

- It renders as HTML in most converters.
- Great for spoilers or extra info.

</details>
