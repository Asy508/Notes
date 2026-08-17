# Markdown Syntax Guide

Quick reference for common Markdown formatting used in GitHub.

---

# 1. Headings

### Markdown

```markdown
# Heading 1
## Heading 2
### Heading 3
#### Heading 4
```

### Result

# Heading 1
## Heading 2
### Heading 3
#### Heading 4

---

# 2. Bullet List

### Markdown

```markdown
- Item 1
- Item 2
- Item 3
```

### Result

- Item 1
- Item 2
- Item 3

---

# 3. Nested Bullet List

Use spaces before the `-`.

### Markdown

```markdown
- Server
  - Linux
  - Windows
  - Docker
- Network
  - Router
  - Cloudflare
```

### Result

- Server
  - Linux
  - Windows
  - Docker
- Network
  - Router
  - Cloudflare

---

# 4. Numbered List

### Markdown

```markdown
1. Install Ubuntu
2. Install Nginx
3. Install PHP
4. Install WordPress
```

### Result

1. Install Ubuntu
2. Install Nginx
3. Install PHP
4. Install WordPress

---

# 5. Nested Numbered List

### Markdown

```markdown
1. Server
   1. Install Linux
   2. Install Nginx
2. Database
   1. Install MariaDB
   2. Create database
```

### Result

1. Server
   1. Install Linux
   2. Install Nginx
2. Database
   1. Install MariaDB
   2. Create database

---

# 6. Mixed Lists

You can mix bullet and numbered lists.

### Markdown

```markdown
1. Server
   - Linux
   - Nginx
   - PHP
2. Database
   - MariaDB
   - MySQL
3. Network
   - Cloudflare
   - MQTT
```

### Result

1. Server
   - Linux
   - Nginx
   - PHP
2. Database
   - MariaDB
   - MySQL
3. Network
   - Cloudflare
   - MQTT

---

# 7. Checklist

### Markdown

```markdown
- [ ] Install Ubuntu
- [ ] Install Nginx
- [ ] Install MariaDB
- [x] Install Git
```

### Result

- [ ] Install Ubuntu
- [ ] Install Nginx
- [ ] Install MariaDB
- [x] Install Git

`[ ]` = unchecked

`[x]` = checked

---

# 8. Tree / Folder Structure

Markdown itself does not have a special "tree" syntax.

Use a code block with `text`.

### Markdown

````markdown
```text
server-notes/
├── README.md
├── Git/
│   ├── github-commands.md
│   └── markdown-syntax.md
├── Linux/
│   ├── nginx.md
│   └── wordpress.md
├── Cloudflare/
│   └── cloudflared.md
└── MQTT/
    └── mqtt.md
```

# 9. Markdown Links and Folder Access

### Link to a File in the Same Folder

Example structure:

```text
server-notes/
├── README.md
└── Git/
    ├── github-commands.md
    └── markdown-syntax.md
```
From github-commands.md, link to markdown-syntax.md:
```
[Markdown Guide](markdown-syntax.md)
```

### Link to a File in a Parent Folder
-Use ../ to go up one folder.
-Example:
```
server-notes/
├── README.md
└── Git/
    └── github-commands.md
```
From github-commands.md, go back to README.md:
```
[Home](../README.md)
```
`..` means: 
`Go up one folder`