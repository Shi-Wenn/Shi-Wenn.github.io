---
title: 使用 Obsidian+github搭建博客的要点
date: 2025-02-01
categories:
  - 探索笔记
tags:
  - 软件工具
  - Obsidian
  - Github
  - Workflow
  - 个人博客搭建
---

### 需求目标

自从有道云笔记开始限制登录设备数量，我一直想转移阵地，作为一个笔记重度用户，信赖写下的东西胜过自己的脑子。被 Obsidian 强大的插件和 md 的灵活性吸引，终于想法设法导出了全部内容。既有工具，又怎么能忍住不用，跃跃欲试想要建个既能自己本地创作存储，又方便分享的工作流。设想中的需求包含以下几项：

第一，必须能够本地存储，我已经受够了有道云笔记的封闭，用各种方法限制迁移，难以以开放性格式导出，这是我放弃它转向开源的 Obsidian的主要原因。

第二，同时我又希望将自己的文章可以跟有相似兴趣的伙伴分享，这就需要将内容上传云端。

第三，我期望这个编辑和发布的流程尽可能简单，保证数据安全的同时也要能够方便的修改。经过和AI的讨论决定用以下流程搭建自己的博客创作工作流。

 最终确定选用的工具和基本流程是：在 Obsidian 写 Markdown 文档，用 GitHub Desktop 提交文件自动同步到博客仓库，GitHub Pages + Jekyll（静态站点生成器，github 自带）自动生成网页，博客自动更新。

---

### 所需软件工具：

- Obsidian：客户端软件，负责写

- GitHub Desktop： github 的图形化界面客户端软件，负责将 Obsidian 更新的内容推送到 github

- GitHub Pages 是 github 自带的功能，原生支持 Jekyll，可以将仓库内容自动部署并发布为静态博客

---

### 实现流程

#### 第一步：创建新的博客仓库

打开 GitHub → 点右上角 “New repository”

名称：yourname.github.io

使用 GitHub Pages 的默认域名格式，会省很多配置。

勾选：
 ✔ Public
 ✔ Add a README（可选）

创建完成后先放着。

#### 第二步：在仓库里启用 GitHub Pages

进入仓库 → Settings → Pages

“Source” 选择：**Deploy from a branch（从分支部署）**

branch 选 **main**

folder 选 **root**

保存后会出现一个网址：https://yourname.github.io 就是博客网址。

#### 第三步：配置博客主题和首页

在 GitHub 页面上：

1. 点 “Add file” → “Create new file”
    
2. 文件名填：  **_config.yml**
    
3. 内容先写最基础的：

```
title: Shi-Wenn Blog
description: My personal blog powered by Jekyll
theme: minima
```

theme: minima 可以换成其他主题

点击 “Commit new file”， 完成后，这个仓库就正式变成一个 Jekyll 项目。

可以手动再加一个文件：index.md，这是博客首页

内容随便写一点，保存并 commit。

#### 第四步：把仓库克隆到本地

使用 GitHub Desktop：

File → Clone repository → 选择刚才的仓库。

#### 第五步：在仓库里创建文章目录

在本地仓库中新建一个文件夹：_posts

Jekyll 固定规则：所有博客文章都放在 _posts 文件夹里。

#### 第六步：在 Obsidian 里打开这个仓库

Obsidian → Open folder as vault → 选择克隆下来的仓库。

之后就可以在 Obsidian 里写文章了。

#### 第七步：用 Obsidian 写文章

在 _posts 文件夹里新建一个文件，文件名格式必须严格遵守 Jekyll 规范：

> 2025-02-01-hello-world.md

文件内容示例：

```
---
title: Hello World
date: 2025-02-01
categories: [随笔]
tags: [测试]
---

这是我的第一篇博客文章。
我正在用 Obsidian 写作，然后用 GitHub Pages 发布。
```

写完保存。

#### 第八步：用 GitHub Desktop 提交文章并推送

GitHub Desktop → Summary 随便写点 → Commit to main → Push origin。

推送后等十几秒刷新网站：https://yourname.github.io

刚刚写的文章就会显示出来。

---

### 注意事项

#### 关于 Jekyll

- 必须在 _posts 文件夹里新建博客文件，
- 文件名格式必须严格遵守 Jekyll 规范，参考：

 > 2025-02-01-hello-world.md

- 发布文章需要在头部增加一个信息块，这样才能识别为博客文章

```
---
title: 我的第一篇博客
date: 2025-02-01
categories: [生活]
tags: [感想]
---

这里是正文……
```

#### Github Desktop 的逻辑

可以看作一个“把本地文件夹和远程仓库绑在一起的同步工具”，三个核心动作：

- **Clone（克隆）**：把 GitHub 的云仓库复制到本地电脑的一个文件夹里。
- **Commit（提交）**：把本地本次修改打个“快照”。
- **Push（推送）**：把快照上传到 GitHub，让网站及时更新。

总结，写博客的一整套动作是：
 在 Obsidian 修改文件 → 保存
 打开 GitHub Desktop → Commit → Push
 博客自动更新

#### 忽略 Obsidian 配置的方法

用 Obsidian 打开克隆到本地的仓库，Obsidian 会在文件夹里创建一个 .obsidian 文件夹，这里面包含 Obsidian 设置、主题、插件配置。*(默认不显示，但在 Finder 中按 **⌘ + Shift + .** 显示隐藏文件，就可以看到）*

虽然这些配置文件 推到 GitHub 不会影响 Jekyll 博客渲染，因为：

1. Jekyll 会忽略以点号开头（dotfiles）的文件夹，例如 .obsidian
    
2. 即便上传，也不会进入 _posts、index.md，不影响网页内容
    
3. 最坏情况只是让仓库里多一点“杂物”，但不触发任何部署问题

但如果希望仓库更干净，可以给博客仓库加 .gitignore，忽略 Obsidian 配置，具体做法是：

在仓库根目录新建 .gitignore（可以用 vscode 打开仓库并新建文件）：

```
.obsidian/
.DS_Store
```

这样 Obsidian 配置文件就不会被推到 github 仓库里了。

#### 文章中插入图片的注意事项

1. 为了方便博客中的图片管理，建议在仓库里固定一个统一的图片目录 assets/images，所有图片统一放置在该文件夹中。在 obsidian 的设置中做如下设置，这样在文档中粘贴的图片将自动进入 images 文件夹（否则默认是根目录）
2. 关闭默认的 wikilink 链接格式，因为此格式无法被 Github pages 和 Jekyll 识别，会导致图片无法显示。

![example](assets/images/Pasted%20image%2020251202211851.png)
