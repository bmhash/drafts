# Quick Start Guide - Blog Setup & Daily Workflow

## 🚀 One-Time GitLab Setup (Run These Commands)

### 1. Create GitLab Project
1. Go to https://gitlab.com
2. Click **New project** → **Create blank project**
3. Name it `blog` (or `<your-username>.gitlab.io` for root domain)
4. Set to **Public** for free GitLab Pages
5. Click **Create project**

### 2. Connect & Push Your Blog
```bash
cd /home/bhash/repos/blog

# Add your GitLab remote (replace <your-username> with your actual GitLab username)
git remote add origin https://gitlab.com/<your-username>/blog.git

# Switch to main branch (GitLab Pages expects 'main')
git branch -M main

# Push everything to GitLab
git push -u origin main
```

### 3. Wait for Deployment
- Go to your GitLab project → **Build** → **Pipelines**
- Wait for the pipeline to complete (green checkmark)
- Go to **Deploy** → **Pages** to find your live URL

---

## ✍️ Daily Posting Workflow

### Create a New Post
```bash
cd /home/bhash/repos/blog

# Create new post (replace date and title)
hugo new posts/2026-02-16-my-topic.md
```

### Edit the Post
Open `content/posts/2026-02-16-my-topic.md` and:
1. Set `draft = false`
2. Update the title
3. Write your content in Markdown

Example:
```markdown
+++
title = "My Deep Work Session"
date = 2026-02-16T08:00:00+00:00
draft = false
+++

Today I completed 3 hours of focused work on...
```

### Publish the Post
```bash
# Add, commit, and push
git add content/posts/2026-02-16-my-topic.md
git commit -m "post: my deep work session"
git push
```

**That's it!** Your post will be live in 1-2 minutes.

---

## 🔧 Local Preview (Optional)
```bash
cd /home/bhash/repos/blog
hugo server --baseURL="http://localhost:1313/"
# Visit http://localhost:1313/
```

---

## 🛠️ Maintenance & Upkeep

### Monthly Tasks
- **Update Hugo theme**: `git submodule update --remote themes/PaperMod`
- **Check for Hugo updates**: `hugo version` and update if needed

### Troubleshooting
- **Pipeline fails**: Check **Build** → **Pipelines** for error logs
- **Site not updating**: Ensure `draft = false` in your posts
- **Theme issues**: Run `git submodule update --init --recursive`

### Backup Strategy
Your blog is automatically backed up to GitLab. To clone elsewhere:
```bash
git clone --recurse-submodules https://gitlab.com/<your-username>/blog.git
```

---

## 📝 Markdown Tips for Posts

```markdown
# Heading 1
## Heading 2

**Bold text**
*Italic text*

- Bullet point
- Another point

1. Numbered list
2. Second item

[Link text](https://example.com)

`inline code`

```code
Code block
```

> Quote block
```

---

## 🎯 Quick Commands Reference

```bash
# Create post
hugo new posts/YYYY-MM-DD-title.md

# Local preview
hugo server --baseURL="http://localhost:1313/"

# Publish
git add content/posts/your-file.md
git commit -m "post: your title"
git push

# Update theme
git submodule update --remote themes/PaperMod
git add themes/PaperMod
git commit -m "update: theme"
git push
```
