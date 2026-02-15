# Hugo + GitLab Pages Setup Guide

This is a complete, self‑contained guide for creating a minimal **Hugo** blog and deploying it automatically to **GitLab Pages** using **GitLab CI**.  
You can save this file as `README.md` at the root of your project.

---

## 0. Prerequisites

You need the following before you start:

- A GitLab account.
- Git installed locally.
- Hugo (extended) installed locally.

To verify that Hugo (extended) is installed:

```bash
hugo version
```

You should see something like:

```text
hugo v0.xx.x-xxxxxxx+extended linux/amd64 BuildDate=...
```

If it does not say `extended`, reinstall using the extended build for your platform.

## 1. Create a New Hugo Site Locally

Choose a directory where you keep your projects and create a new Hugo site:

```bash
cd ~/code            # or any directory you prefer
hugo new site myblog
cd myblog
```

Initialize a Git repository and ignore the Hugo build output:

```bash
git init
echo "/public" >> .gitignore
git add .
git commit -m "chore: initial hugo site"
```

At this point, you have a bare Hugo site with no theme and no content.

## 2. Add a Minimal Theme (PaperMod Example)

Go to https://themes.gohugo.io and choose a minimal theme.
This guide uses PaperMod as an example, but you can substitute any other theme.

Add the theme as a Git submodule:

```bash
git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
```

Create or edit `hugo.toml` (or `config.toml` if you prefer that filename) with the following minimal configuration:

```text
baseURL = "https://<your-namespace>.gitlab.io/<project-path>/"
languageCode = "en-us"
title = "My Hugo Blog"
theme = "PaperMod"

[pagination]
pagerSize = 10
```

Replace:

- `<your-namespace>` with your GitLab username or group name.
- `<project-path>` with the GitLab project name that you will create (for example, `myblog`).

Commit the theme and configuration:

```bash
git add .
git commit -m "feat: add PaperMod theme and basic config"
```

## 3. Create Your First Post

Use Hugo to generate a new post file:

```bash
hugo new posts/first-post.md
```

Open `content/posts/first-post.md` in your editor and replace its contents with:

```text
+++
title = "First Post"
date = 2026-02-15T22:00:00+00:00
draft = false
+++

This is my first deep work note.
```

Make sure `draft = false` so the post is included in production builds.

## 4. Preview the Site Locally

Run the Hugo development server:

```bash
hugo server -D
```

Then open this URL in your browser:

```text
http://localhost:1313
```

You should see your site with the theme applied and your first post listed.

Keep this running while you experiment; Hugo will live‑reload changes.

## 5. Create a GitLab Project and Push Your Site

Now you'll connect your local repo to GitLab.

### 5.1 Create the Project on GitLab

1. Log in to GitLab.
2. Click **New project**.
3. Choose **Create blank project**.
4. Set **Project name** to `myblog` (or any name you like).
5. Leave **Visibility** as **Public** if you want a public blog.
6. Click **Create project**.

Copy the repository's HTTPS URL, which will look similar to:

```text
https://gitlab.com/<your-namespace>/myblog.git
```

### 5.2 Add the Remote and Push

In your local `myblog` directory:

```bash
git remote add origin https://gitlab.com/<your-namespace>/myblog.git
git branch -M main
git push -u origin main
```

Your Hugo site and theme configuration are now stored in GitLab.

## 6. Add GitLab CI Configuration for Hugo + Pages

GitLab Pages is driven by a CI job named `pages` that publishes the `public/` directory.
You will configure a CI pipeline that:

1. Builds the Hugo site into `public/`.
2. Publishes `public` as a GitLab Pages artifact.

Create a file named `.gitlab-ci.yml` in the root of your project with the following contents:

```yaml
image: registry.gitlab.com/pages/hugo/hugo_extended:latest

variables:
  GIT_SUBMODULE_STRATEGY: recursive

stages:
  - build
  - deploy

build:
  stage: build
  script:
    # Optional: show Hugo version for debugging
    - hugo version
    # Build the site into public/ using the GitLab Pages URL
    - hugo --minify --baseURL "${CI_PAGES_URL}"
  artifacts:
    paths:
      - public
  only:
    - main

pages:
  stage: deploy
  script:
    - echo "Deploying to GitLab Pages from public/"
  artifacts:
    paths:
      - public
  needs:
    - build
  rules:
    - if: '$CI_COMMIT_BRANCH == "main"'
```

**Explanation of key parts:**

- `image: registry.gitlab.com/pages/hugo/hugo_extended:latest`  
  Uses the official Hugo Extended Docker image maintained for GitLab Pages.

- `GIT_SUBMODULE_STRATEGY: recursive`  
  Ensures your theme submodule is checked out during the build.

- `build` job  
  Runs `hugo` to produce the static site in `public/` and stores `public/` as an artifact.

- `pages` job  
  Publishes the `public` artifact as your GitLab Pages site.

Commit and push the CI file:

```bash
git add .gitlab-ci.yml
git commit -m "ci: add gitlab pages pipeline for hugo"
git push
```

## 7. Wait for the Pipeline and Access Your Site

After pushing, GitLab will automatically start a pipeline.

### 7.1 Check the Pipeline

1. In your GitLab project, go to **Build** → **Pipelines**.
2. You should see a pipeline triggered for branch `main`.
3. Wait until all jobs are marked as **passed**.
4. If something fails, click the job to read the logs (common issues are typos in `.gitlab-ci.yml` or missing theme configuration).

### 7.2 Find the GitLab Pages URL

1. In your project, go to **Deploy** → **Pages**.
2. You will see a URL similar to:

```text
https://<your-namespace>.gitlab.io/myblog/
```

3. Click it, and your Hugo site should be live on GitLab Pages.

## 8. Daily "Git‑Like" Posting Workflow

Once everything is wired up, your daily workflow is just:

1. Create a new Markdown file with `hugo`.
2. Edit the content.
3. Commit and push.
4. Let GitLab build and deploy.

### Example: Create a New Deep Work Note

```bash
cd ~/code/myblog

# 1. Create a new post
hugo new posts/deep-work-2026-02-16.md
```

Edit `content/posts/deep-work-2026-02-16.md`:

```text
+++
title = "Deep Work Log – 2026‑02‑16"
date = 2026-02-16T08:00:00+00:00
draft = false
+++

Today I focused on a 3‑hour deep work block without social media.
```

Commit and push:

```bash
git add content/posts/deep-work-2026-02-16.md
git commit -m "post: deep work 2026-02-16"
git push
```

GitLab will automatically:

1. Run the `build` job (Hugo build).
2. Run the `pages` job (publish).
3. Update the site at `https://<your-namespace>.gitlab.io/myblog/`.

Within a minute or two, your new post is live.

## 9. Optional: Use Root username.gitlab.io Instead of /myblog

If you prefer your site at:

```text
https://<your-namespace>.gitlab.io/
```

instead of `https://<your-namespace>.gitlab.io/myblog/`, do this:

1. On GitLab, create or rename the project so its name is:

```text
<your-namespace>.gitlab.io
```

For example, if your username is `joao`, use `joao.gitlab.io` as the project name.

2. Update `hugo.toml`:

```text
baseURL = "https://<your-namespace>.gitlab.io/"
```

3. Commit and push:

```bash
git add hugo.toml
git commit -m "chore: set baseURL to root pages URL"
git push
```

GitLab will rebuild and your site will now be available at:

```text
https://<your-namespace>.gitlab.io/
```

## 10. Summary of Commands

For quick reference, here is the complete command sequence you typically run (excluding edits in your text editor):

```bash
# one‑time setup
cd ~/code
hugo new site myblog
cd myblog
git init
echo "/public" >> .gitignore
git add .
git commit -m "chore: initial hugo site"

git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
# (edit hugo.toml as in this guide)
git add .
git commit -m "feat: add PaperMod theme and basic config"

hugo new posts/first-post.md
# (edit content/posts/first-post.md, set draft = false)
git add content/posts/first-post.md
git commit -m "post: first post"

git remote add origin https://gitlab.com/<your-namespace>/myblog.git
git branch -M main
git push -u origin main

# add CI
# (create .gitlab-ci.yml as in this guide)
git add .gitlab-ci.yml
git commit -m "ci: add gitlab pages pipeline for hugo"
git push

# later, whenever you want to post
cd ~/code/myblog
hugo new posts/some-new-post.md
# (edit file, set draft = false)
git add content/posts/some-new-post.md
git commit -m "post: some new post"
git push
```
