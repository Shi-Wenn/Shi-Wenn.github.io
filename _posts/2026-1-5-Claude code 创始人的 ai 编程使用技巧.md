---
title: Claude code 创始人的 ai 编程使用技巧
date: 2026-1-5
categories:
  - 转载
tags:
  - AIGC
  - Claude code
  - 工程思维
---


### 核心要点：

- 直接选择最好的思考模型。准确比速度重要。
- 让 Claude.md 成为一个在项目运行过程中逐渐完善丰富的==动态文档==  。过程中取得的所有经验/规则/失误都能沉淀到这一核心文档中。可以直接要求 Claude 将发现的问题加入 Claude.md 文档中，在项目行进过程中不断优化它。
- 识别重复的工作流，储存成斜杠命令
- 将常见的工作流做成 subagents，自动化。
- 维护储存常用配置的文件.claude/settings.json，团队共享。
- ==先计划再行动。==从 plan 模式开始，讨论到满意，然后切换到自动接收编辑模式。
- 给 Claude 一个==验证==工作的方法，形成反馈循环。

[Boris Cherny 原文链接](https://x.com/bcherny/status/2007179832300581177?s=20)

### 原文翻译
我是 Boris，Claude Code 是我开发的。很多人问我如何使用 Claude Code，所以我想简单展示一下我的配置。 我的配置可能出乎意料地简单！Claude Code 开箱即用，效果非常好，所以我个人很少对其进行自定义。使用 Claude Code 没有唯一正确的方法：我们特意将其设计成可以随意使用、自定义和修改的。Claude Code 团队的每位成员使用它的方式都各不相同。 好了，开始吧。

1/ 我在终端中并行运行 5 个 Claude 进程。我将标签页编号为 1-5，并使用系统通知来了解何时某个 Claude 进程需要输入
![](assets/images/Pasted%20image%2020260105185400.png)

2/ 除了本地的 Claude 实例外，我还在 [http://claude.ai/code](https://t.co/pEWPQoSq5t) 上运行 5-10 个 Claude 实例 。我在终端编写代码时，经常会将本地会话切换到 Web 端（使用 & ），或者在 Chrome 浏览器中手动启动会话，有时还会使用 --teleport 参数在本地和 Web 端之间来回切换。此外，我每天早上和白天都会从手机（通过 Claude iOS 应用）启动几个会话，之后再查看它们的运行情况。
![](assets/images/Pasted%20image%2020260105185614.png)

3/ 我使用 Opus 4.5 并结合 Thinking for Everything。这是我用过的最好的编码模型，虽然它比 Sonnet 更大更慢，但由于它需要的干预更少，而且工具使用更高效，所以最终它几乎总是比使用较小的模型更快。

4/ 我们团队共享一个用于 Claude Code 代码库的 CLAUDE.md 文件。我们会将其提交到 Git，整个团队每周都会多次贡献代码。每当我们发现 Claude 做错了什么，我们都会将其添加到 CLAUDE.md 中 ，以便 Claude 下次避免犯同样的错误。

5/ 在代码审查期间，我经常会在同事的 PR 中标记 @.claude，以便将一些内容添加到 CLAUDE.md 文件中。我们使用 Claude Code Github action (/install-github-action) 来实现这一点。这是我们版本的@danshipper 复合工程

6/ 大多数会话都从计划模式（Shift+Tab 两次）开始。如果我的目标是提交一个 Pull Request，我会使用计划模式，然后和 Claude 反复沟通，直到我对计划满意为止。之后，我会切换到自动接受修改模式，Claude 通常可以一次性完成。一个好的计划真的非常重要！
![](assets/images/Pasted%20image%2020260105190337.png)

7/ 我在所有需要每天多次执行的“内部循环”工作流程中都使用斜杠命令。这避免了我重复输入命令，也使得 Claude 也能使用这些工作流程。这些命令已提交到 git 仓库，并位于 .claude/commands/ 目录下。 例如，我和 Claude 每天都会使用数十次 `/commit-push-pr` 斜杠命令。该命令使用内联 bash 预先计算 git 状态和其他一些信息，以加快命令运行速度，并避免与模型来回交互 [（ https://code.claude.com/docs/en/slash-commands#bash-command-execution…](https://t.co/4jZ7RK0suT) 。

8/ 我经常使用一些子代理：code-simplifier 会在 Claude 完成工作后简化代码，verify-app 会提供详细的端到端 Claude 代码测试指南，等等。类似于斜杠命令，我认为子代理的作用是自动化我处理大多数 PR 时最常用的工作流程。
![](assets/images/Pasted%20image%2020260105190538.png)

9/ 我们使用 PostToolUse 钩子来格式化 Claude 生成的代码。Claude 通常能自动生成格式良好的代码，而该钩子会处理最后 10% 的代码，以避免后续在持续集成 (CI) 过程中出现格式错误。
![](assets/images/Pasted%20image%2020260105190634.png)

10/ 我不使用 `--dangerously-skip-permissions` 参数。相反，我使用 `/permissions` 参数预先允许一些我知道在我的环境中安全的常用 bash 命令，以避免不必要的权限提示。这些配置大部分都已提交到 `.claude/settings.json` 文件中，并与团队共享。
![](assets/images/Pasted%20image%2020260105190723.png)

11/ Claude Code 为我使用所有工具。它经常搜索信息并发布到 Slack（通过 MCP 服务器），运行 BigQuery 查询来回答分析问题（使用 bq CLI），从 Sentry 获取错误日志等等。Slack MCP 配置已添加到我们的 .mcp.json 文件中，并与团队共享。

12/ 对于耗时很长的任务，我将采取以下三种方法之一：(a) 任务完成后，提示 Claude 使用后台代理验证其运行情况；(b) 使用代理的停止钩子以更确定的方式进行验证；或 (c) 使用 ralph-wiggum 插件（最初由 GeoffreyHuntley 构思）。我还会在沙盒中使用 --permission-mode=dontAsk 或 --dangerously-skip-permissions 来避免会话的权限提示，这样 Claude 就可以不受我的限制地进行工作。

13/ 最后一点建议：要想让 Claude Code 发挥最佳效果，最重要的一点可能是——给 Claude 提供验证其运行结果的方法。如果 Claude 拥有这种反馈机制，最终结果的质量将提升 2-3 倍。 Claude 会测试我落地的每一个变化。
