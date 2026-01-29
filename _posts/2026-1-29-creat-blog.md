---
layout: post
title: 如何创建属于自己的个人博客？
subtitle: 用 GitHub Pages + Huxpro 模板搭建个人博客的踩坑与复盘
author: "zhuang"
header-style: text
tags:
  - Web
---

# 用 GitHub Pages + Huxpro 模板搭建个人博客的踩坑与复盘

作为一个经常折腾技术的开发者，我一直想拥有一个简洁美观的个人博客。这次终于用 **GitHub Pages + Huxpro 模板** 完成了搭建，但过程中踩了不少 Git 操作和配置的坑。这篇文章记录完整流程，也总结一些容易踩的雷，希望能帮到同样想搭博客的朋友。

## 一、前期准备：理解 GitHub Pages 的核心逻辑

在动手前，先明确基础概念：

- **GitHub Pages**：是 GitHub 提供的静态页面托管服务，只需将静态文件（HTML/CSS/JS）推送到指定仓库（`用户名.github.io`），就能自动生成公开访问的博客页面。
- **Huxpro 模板**：是一个基于 Jekyll 的博客模板（作者是前 Meta React 团队的 Hux 黄玄），自带响应式布局、侧边栏、标签系统等功能，不用从零写前端代码。

## 二、搭建过程：从仓库创建到模板整合

### 1. 第一步：创建 GitHub Pages 仓库

首先在 GitHub 新建一个仓库，命名为 `用户名.github.io`（比如我的是 `zhuangyiqiao.github.io`），这是 GitHub Pages 的固定仓库名，创建后默认分支为 `main`（注意：2020 年后 GitHub 已将默认分支从 `master` 改为 `main`，后续操作要统一用 `main`）。

### 2. 第二步：克隆仓库到本地（或 Codespaces）

我用的是 GitHub Codespaces（在线开发环境），直接克隆仓库：

bash

运行

```
# 克隆自己的仓库到本地
git clone https://github.com/zhuangyiqiao/zhuangyiqiao.github.io.git
cd zhuangyiqiao.github.io
```

### 3. 第三步：引入 Huxpro 模板（核心踩坑点）

这一步是最容易出错的，我一开始犯了几个典型错误：

- **错误 1：直接在仓库根目录克隆模板**
    
    一开始我直接在自己的仓库里克隆 Huxpro 模板，导致模板文件被放到子文件夹里，没有整合到仓库根目录，页面只显示初始的 `Hello World`。
    
    **正确操作**：先把模板克隆到临时目录，再复制核心文件到自己的仓库：
    
    bash
    
    运行
    
    ```
    # 克隆 Huxpro 模板到临时目录
    git clone https://github.com/Huxpro/huxpro.github.io.git /workspaces/hux-temp
    
    # 复制模板所有文件（包括隐藏文件）到自己的仓库根目录
    cp -r /workspaces/hux-temp/* ./
    cp -r /workspaces/hux-temp/.* ./
    
    # 删除临时目录
    rm -rf /workspaces/hux-temp
    ```
    
- **错误 2：重复添加远程别名 + 分支名不匹配**
    
    我错误地添加了多余的远程别名（比如 `mine`），还试图推送到不存在的 `master` 分支，导致报错 `src refspec master does not match any`。
    
    **正确操作**：只用默认的 `origin` 别名，推送时指定 `main` 分支：
    
    bash
    
    运行
    
    ```
    # 提交模板文件
    git add .
    git commit -m "引入 Huxpro 博客模板"
    
    # 推送到自己的仓库（默认分支 main）
    git push origin main
    ```
    

### 4. 第四步：修改模板配置为个人信息

打开模板的 `_config.yml` 文件，替换所有 Huxpro 的信息为自己的：

yaml

```
# 核心配置修改
title: 庄一桥的技术博客
SEOTitle: 庄一桥的技术博客 | 记录开发与思考
email: 你的邮箱@example.com
description: "一名开发者的技术记录与成长分享"
url: "https://zhuangyiqiao.github.io"  # 固定为自己的 GitHub Pages 地址
github_username: zhuangyiqiao
sidebar-about-description: "热爱技术的开发者 | 专注前端与工程化"
sidebar-avatar: https://github.com/zhuangyiqiao.png  # 自己的头像 URL
```

## 三、踩坑复盘：这些错误一定要避免

1. **不要在命令前加 `$`**：`$` 是终端提示符，不是命令的一部分，直接输入 `git clone` 即可。
2. **分支名要统一用 `main`**：GitHub 新仓库默认分支是 `main`，不要习惯性用 `master`，否则会提示分支不存在。
3. **不要重复添加远程别名**：默认的 `origin` 已经指向自己的仓库，新增别名（比如 `mine`）只会增加混淆。
4. **必须复制模板的隐藏文件**：模板的 `.gitignore`、`_config.yml` 等隐藏文件是核心，漏掉会导致博客样式失效。
5. **不要直接克隆模板到自己的仓库根目录**：会生成子文件夹，必须通过临时目录复制文件到根目录。

## 四、最后一步：发布与验证

推送完成后，等待 1-5 分钟（GitHub Pages 构建需要时间），打开 `https://用户名.github.io`，就能看到完整的博客页面了。如果页面没更新，可以在仓库的 `Settings → Pages` 中手动触发重新构建。

## 写在最后

这次搭建博客的过程，本质是对 Git 操作和静态页面托管的一次实践。虽然踩了不少坑，但也理清了 GitHub Pages 的核心逻辑 ——**其实就是 “推送静态文件到指定仓库”**，模板只是帮我们省去了写前端的时间。

如果你也想搭博客，建议先理清仓库、分支、远程别名的概念，再动手操作，能少走很多弯路。后续我会在这个博客上分享更多技术内容，欢迎关注～