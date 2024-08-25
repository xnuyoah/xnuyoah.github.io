+++
title = '使用 Hugo + Github Pages 搭建个人博客(windows 版本)'
date = 2024-08-25T14:39:01+08:00
draft = true
categories = ["随笔"]
tags = ["Hugo", "Github Pages"]
+++
## Hugo 下载
前往 [Hugo Tags](https://github.com/gohugoio/hugo/tags) 选择 `hugo_extended_xxx_windows-amd64.zip` 版本下载后并解压

## Hugo 解压安装

### `win + s` 搜索 `环境变量`
点击 `编辑环境变量`，依次打开 `环境变量 > 系统变量 > Path > 新建` 并将 Hugo 解压路径添加进去

### 查看 hugo 版本
```bash
hugo version

# 安装成功输出示例: hugo xxx
```

## 搭建个人博客

### 创建博客

- 打开 `cmd` 窗口
```bash
hugo new site blogs
```

- 本地预览
```bash
hugo server -D

# 访问地址: http://localhost:1313/
```

### 配置主题
配置主题时对主题进行修改可能会产生冲突，可以先 `fork` 主题仓库到自己的账户，使用 `git submodule` 对修改后的主题进行维护

- 使用 `git submodule`
```bash
cd blogs
git init
git submodule https://github.com/xnuyoah/hugo-theme-stack themes/hugo-theme-stack
```

- 复制 `exampleSite` 目录到主目录下然后修改配置
```bash
cp -rf themes/hugo-theme-stack/exampleSite/* ./
```

## 修改配置文件

### hugo.yaml 文件配置项
```yaml
baseurl: https://example.com  # 网站链接
languageCode: zh-cn  # 语言
theme: hugo-theme-stack  # 主题名
paginate: 3  # 分页数
title: xxx  # 网站标题
copyright: xxx  # 页脚 copyright
DefaultContentLanguage: zh-cn  # 默认语言
hasCJKLanguage: true  # 设置默认语言为 zh-cn 需要将此设为 true
languages:  # 语言配置
  zh-cn:
    languageName: 简体中文
    title: xxx  # 标题
    weight: 2
    params:
      description: xxx  # 描述

params:
  mainSections:
    - post  # 博客文章识别目录名
  favicon: static/favivon.ico  # 网站图标

  footer:  # 页脚配置
    since: 2024
    customText: xxx

  dateFormat:  # 时间格式配置
    published: Jan 02, 2006
    lastUpdated: Jan 02, 2006 15:04 MST

  sidebar:
    subtitle: xxx  # 子标题
    avatar:
      src: img/avatar.png  # 头像

  article:
    license:
      enabled: false  # 是否显示 license
      default: Licensed under CC BY-NC-SA 4.0
```

### `page` 目录修改

- 删除目录 `about`，`link`
- 修改 `archives`，`search` 目录下的 `title` 值为中文

### 注释非必要提示
注释 `themes > hugo-theme-stack > layouts > footer` 下 `footer.html` 部分内容
```html
<!-- {{- $Generator := `<a href="https://gohugo.io/" target="_blank" rel="noopener">Hugo</a>` -}}
{{- $Theme := printf `<b><a href="https://github.com/CaiJimmy/hugo-theme-stack" target="_blank" rel="noopener" data-version="%s">Stack</a></b>` $ThemeVersion -}}
{{- $DesignedBy := `<a href="https://jimmycai.com" target="_blank" rel="noopener">Jimmy</a>` -}}
{{ T "footer.builtWith" (dict "Generator" $Generator) | safeHTML }} <br />
{{ T "footer.designedBy" (dict "Theme" $Theme "DesignedBy" $DesignedBy) | safeHTML }} -->
```

## 创建文章

### 使用 hugo 创建文章，生成 MarkDown 文档
```bash
hugo new post/xxx/index.md
```
### MarkDown 文档元数据

- categories：分类
- tags：标签
- image：文章封面
- draft：是否草稿

### 预览文章
```bash
hugo server -D
```

## Github 部署

### 新建特殊仓库 `xxx.github.io` 部署博客
使用命令 `hugo -D` 生成 `public` 目录

#### 上传 `public` 目录
```bash
git init
git add .
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/xxx/xxx.git
git push -u origin main
```

#### `Pages` 部署
配置完成访问 `https://xxx.github.io`

### 新建仓库存放源数据并配置 Github Action 自动部署

#### 添加 `.gitignore`
```bash
public
resources
.hugo_build.lock
```

#### 创建部署 `token`
- 创建部署 `token` 并勾选 `repo`，`workflow` 选项
- 前往特殊仓库 `xxx.github.io` 设置部署 `token`

#### 创建 `.github/workflows/xxx.yaml` 文件
```yaml
name: hugo deploy

on:
  push:
    branches:
      - main  # 设置部署分支

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
        - name: Checkout
          uses: actions/checkout@v4
          with:
              submodules: true  # 获取 hugo themes
              fetch-depth: 0

        - name: Setup Hugo
          uses: peaceiris/actions-hugo@v3
          with:
              hugo-version: "0.133.0"  # hugo 版本
              extended: true  # 启用 extended

        - name: Build Web
          run: hugo -D

        - name: Deploy Web
          uses: peaceiris/actions-gh-pages@v4
          with:
              PERSONAL_TOKEN: ${{ secrets.token变量名 }}
              EXTERNAL_REPOSITORY: github名/仓库名
              PUBLISH_BRANCH: main
              PUBLISH_DIR: ./public
              commit_message: auto deploy
```

## 参考文章
- [使用 Hugo + Github Pages 部署个人博客](https://ratmomo.github.io/p/2024/06/%E4%BD%BF%E7%94%A8-hugo--github-pages-%E9%83%A8%E7%BD%B2%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/)
- [【Hugo】Hugo + Github 免费部署自己的博客](https://letere-gzj.github.io/hugo-stack/p/hugohugo--github-%E5%85%8D%E8%B4%B9%E9%83%A8%E7%BD%B2%E8%87%AA%E5%B7%B1%E7%9A%84%E5%8D%9A%E5%AE%A2/)
- [Hugo + GitHub Action，搭建你的博客自动发布系统](https://www.pseudoyu.com/zh/2022/05/29/deploy_your_blog_using_hugo_and_github_action/)















