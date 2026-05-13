# Claude Code 源码泄露与架构设计调研 + Prompt Engineering

## 基本信息

- 分享时间：2025-05-06
- 周次：Week 1
- 组别：第1组
- 分享人：陈思光、万阳
- 关键词：Claude Code、AI Coding Agent、源码泄露、Agent 架构、Prompt Engineering

## Claude Code 源码泄露与架构设计调研摘要

本报告围绕 Anthropic Claude Code 的起源、发展、源码泄露事件与架构设计展开，指出 Claude Code 的成功并不只是来自 Claude 模型本身，而是来自一整套围绕工程任务执行构建的 agentic coding system。报告梳理了 Claude Code 从 Claude 3.5/3.7 Sonnet、MCP、工具调用能力发展而来的背景，并分析了其从命令行工具演进为软件工程 Agent 平台的过程。源码泄露部分揭示了客户端架构中的关键设计，包括 Agent 循环、工具系统、读写分离并发、Prompt Cache、分层记忆、上下文压缩、权限审查和 Feature Flag 等。整体来看，Claude Code 的核心价值不是“帮用户写一段代码”，而是通过可控、可验证、可持续迭代的工程闭环，帮助开发者推进真实软件工程任务。

## Claude Code 源码泄露与架构设计调研正文

https://tcna2xxx5e8g.feishu.cn/wiki/ABU3wI9jWixMsrkOnPHcFiG7nnc?from=from_copylink


## 提示词工程（Prompt Engineering）摘要
本报告围绕提示词工程（Prompt Engineering）展开，系统梳理了 OpenAI 与 Anthropic 官方推荐的提示词设计原则与实用方法。报告指出，提示词工程的核心是通过清晰、具体、结构化的输入，让大语言模型更稳定地输出符合预期的结果。内容重点介绍了指令前置、上下文补充、格式引导、Few-shot 示例、Prompt Chaining、分隔符与正向指令等常用技巧。整体来看，提示词工程是一种低成本、高通用性的 AI 交互优化方法，适合在日常问答、内容生成、代码辅助和复杂任务拆解等场景中使用。

## 提示词工程（Prompt Engineering）正文
https://ocno6fjgelu7.feishu.cn/docx/BSiFdgwlToDjArxeeUccaZ1HnOe?from=from_copylink

## OpenAI、Anthropic官方提示工程最佳实践 正文
https://ocno6fjgelu7.feishu.cn/docx/QQNFdOrl2oWQ8KxySuOcM8rpnJd?from=from_copylink