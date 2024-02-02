+++
author = "SW"
title = '[流程紀錄] Hugo + Github Page + Github Page 製作個人網站'
date = 2024-02-02T14:15:59+08:00
description = 'Hugo 是一個使用 Go 語言構建的靜態網站生成器。可將Markdown 格式編寫的內容轉換為HTML，生成一個靜態網站。'
tags = [
    "git",
    "github",
    "github page",
    "hugo"]
series = ["Hugo Guide"]
categories = ["流程紀錄"]
+++

自從閱讀了**卡片盒筆記法** & **打造第二大腦**，默默開始將筆記由 Notion 遷移至 Obsidian，也越來越習慣使用 Markdown 來寫筆記。

在 Medium 不支援 Markdown 的情況下，興起了自己試著建一個的念頭..

## Local

1. 安裝 Hugo

- [macOS | Hugo (gohugo.io)](https://gohugo.io/installation/macos/)，使用 `homebrew` 進行安裝

```shell
    brew install hugo
```

2. 創一個資料夾放置相關資料 ex. `blog`

```shell
hugo new site <資料夾名稱>

hugo new site <github帳號.github.io>
#此處命名為github帳號，方便後續使用github page

cd <資料夾名稱>
cd <github帳號.github.io>

git init
```

3. Hugo 框架資料夾結構

   - [Directory structure | Hugo (gohugo.io)](https://gohugo.io/getting-started/directory-structure/)

   ```
    my-site/
    ├── archetypes/  (放置一些md文章的模板)
    │   └── default.md
    ├── assets/
    ├── config/   <-- site configuration
    │   └── _default/
    │       └── hugo.toml <-- site configuration
    ├── content/ (文章放置的位置)
    ├── data/
    ├── i18n/
    ├── layouts/
    ├── public/     <-- created when you build your site
    ├── resources/  <-- created when you build your site
    ├── config.toml(or hugo.toml)/ (一些參數的設定檔)
    ├── static/ (靜態檔案放置的位置(如圖片檔))
    └── themes/ (落格使用的主題放置的位置)
   ```

4. 安裝 blog 主題

- 此次使用` Tailwind` 主題

  - [Tailwind](https://themes.gohugo.io/themes/hugo-theme-tailwind/)

- git submodule 方式安裝

```shell
git submodule add https://github.com/tomowang/hugo-theme-tailwind.git themes/tailwind
```

- 開啟 config.toml(or hugo.toml), 更改 theme to "paper"

```
theme = "paper"
```

5. 啟動 Hugo 伺服器查看網站

```shell
hugo server
#Press `Ctrl + C` to stop Hugo’s development server.
```

6. 嘗試新增 content

```shell
hugo new content posts/my-first-post.md
#Hugo created the file in the `content/posts` directory

```

```
   +++
   title = 'My First Post'
   date = 2024-01-14T07:07:07+01:00
   draft = true
   +++

```

7. 部署時，刪去 draft = true，terminal 輸入`hugo` 產生部署使用的`public`資料夾

## Github

### Github pages

- [Host on GitHub Pages | Hugo (gohugo.io)](https://gohugo.io/hosting-and-deployment/hosting-on-github/)

1.  設定 Github pages

- 在自己的 Github 新增名稱為`<Github的名稱>.github.io`的 repo 然後在本地端的 blog 目錄中加入這個 repo 的 remote 位址

```shell
git remote add origin <repo位址>
```

- 使用 Github pages 的網頁，網址會是.github.io，所以要修改 config.toml，把 url 改成相對應的網址

2. 將資料夾 commit 完後 push 至 Github

```shell
  git add .
  git commit -m "test"

  git push origin

```

### Github action

1.  至 GitHub repository 頁面，選取  **Settings** > **Pages**
    ![](/images/hugo-deploy-01.png)

2.  更改  **Source**  為  `GitHub Actions`
    ![](/images/hugo-deploy-02.png)

3.  在本機資料夾中創造一個空的資料夾`.github`

```
  .github/workflows/hugo.yaml
```

4. 在 hugo.yaml 中貼上下方文字，根據需要修改分支名稱和 Hugo 版本

```YAML
# Sample workflow for building and deploying a Hugo site to GitHub Pages
name: Deploy Hugo site to Pages

on:
	# Runs on pushes targeting the default branch
	push:
		branches:
			- main
	# Allows you to run this workflow manually from the Actions tab
	workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages

permissions:
	contents: read
	pages: write
	id-token: write

# Allow only one concurrent deployment, skipping runs queued between the run in-progress and latest queued.
# However, do NOT cancel in-progress runs as we want to allow these production deployments to complete.
concurrency:
group: "pages"
cancel-in-progress: false


# Default to bash
defaults:
	run:
		shell: bash


jobs:
 # Build job
 build:
	 runs-on: ubuntu-latest
	 env:
		 HUGO_VERSION: 0.121.0
	 steps:
		 - name: Install Hugo CLI
		   run: |
			 wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb \
			 && sudo dpkg -i ${{ runner.temp }}/hugo.deb
		- name: Install Dart Sass
		  run: sudo snap install dart-sass
		# You may remove this step if your site, themes, and modules do not transpile Sass to CSS using the [Dart Sass](https://gohugo.io/hugo-pipes/transpile-sass-to-css/#dart-sass) transpiler.
		- name: Checkout
		  uses: actions/checkout@v4
		  with:
			  submodules: recursive
			  fetch-depth: 0
		- name: Setup Pages
		  id: pages
		  uses: actions/configure-pages@v4
		- name: Install Node.js dependencies
		  run: "[[ -f package-lock.json || -f npm-shrinkwrap.json ]] && npm ci || true"
	    - name: Build with Hugo
		  env:
		  # For maximum backward compatibility with Hugo modules
			  HUGO_ENVIRONMENT: production
			  HUGO_ENV: production
		  run: |
			  hugo \
				  --gc \
				   --minify \
				   --baseURL "${{ steps.pages.outputs.base_url }}/"
		- name: Upload artifact
		- uses: actions/upload-pages-artifact@v2
		- with:
			  path: ./public

# Deployment job
deploy:
	environment:
		name: github-pages
		url: ${{ steps.deployment.outputs.page_url }}
	runs-on: ubuntu-latest
	needs: build
	steps:
		- name: Deploy to GitHub Pages
		- id: deployment
		- uses: actions/deploy-pages@v3
```

5. git commit `Add workflow`，將檔案 push 至 GitHub

6. 在 git hub 首頁點選  **Actions** ，會看到以下畫面，當部署完成燈號會由黃轉綠
   ![](/images/hugo-deploy-03.png)
   ![](/images/hugo-deploy-04.png)

7. 點 `commit message` 可以看到部署完成的網站，未來重新 push 變更時，GitHub 都會重新部署。
   ![](/images/hugo-deploy-05.png)

---

References:

- [Caching dependencies to speed up workflows - GitHub Docs](https://docs.github.com/en/actions/using-workflows/caching-dependencies-to-speed-up-workflows)
- [GitHub Actions documentation - GitHub Docs](https://docs.github.com/en/actions)
