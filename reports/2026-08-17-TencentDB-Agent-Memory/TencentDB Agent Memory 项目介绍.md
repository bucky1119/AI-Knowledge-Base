# TencentDB Agent Memory 项目介绍

> 让 Agent 沉淀经验，让人专注创造。

项目地址：[TencentCloud/TencentDB-Agent-Memory](https://github.com/TencentCloud/TencentDB-Agent-Memory)

## 一、项目概述

TencentDB Agent Memory 是一个面向个人与团队的 AI Agent 长期记忆与知识资产管理平台。

它将 Agent 在工作中产生或使用过的对话、文档、代码和操作经验，整理为可以长期保存、检索、复用、共享和授权的记忆资产。其目标是减少新会话、新 Agent 或新团队成员反复了解项目背景、重新阅读文档和重新摸索工作流程的成本。

与单纯保存聊天记录不同，该项目重点解决三个问题：

1. 哪些信息值得长期保存；
2. 哪些用户或 Agent 有权使用这些信息；
3. 如何在有限的上下文窗口中检索更少、但更准确的内容。

可以将它理解为 AI Agent 团队的“经验库 + 知识库 + 权限控制台”。

## 二、项目要解决的问题

在长期使用 AI Agent 的过程中，经常会遇到以下问题：

- 新会话不了解之前讨论过的项目背景、用户偏好和关键决策；
- 不同 Agent 需要重复阅读相同的文档、代码和历史记录；
- 已经验证过的排障、评审和发布流程无法直接复用；
- 普通 RAG 通常只负责检索内容，缺少资产归属、版本、状态和权限管理；
- 多个 Agent 之间既需要共享经验，又需要保护用户或团队的私有信息。

TencentDB Agent Memory 希望把已经付出的学习成本保存为一份“存档”，让后续 Agent 可以直接从已有成果开始工作。

## 三、四类核心记忆资产

平台将信息统一整理为四类 Memory Asset。

### 1. Chat Memory

Chat Memory 保存用户偏好、事实、约束、项目决策和交互历史，使 Agent 在新会话中仍能理解用户和项目背景。

对话记忆会被逐层提炼：

- **L0 Conversation**：保留完整上下文的原始对话；
- **L1 Atom**：从对话中提取的事实、偏好、约束和事件；
- **L2 Scenario**：围绕项目或场景组织的知识块；
- **L3 Core / Persona**：长期画像、稳定习惯和高层认知。

系统通常先使用 L2、L3 快速恢复上下文；需要精确事实时，再通过 BM25、向量检索和 RRF 融合回查 L1、L0。

### 2. Skill

Skill 用于沉淀已经验证过的工作方法，例如：

- 故障排查流程；
- 代码审查规范；
- 发布检查清单；
- 功能交付流程；
- 特定项目的操作步骤。

这里的 Skill 不只是一个提示词片段，还可以包含版本、资源文件、触发条件、执行步骤和验证规则。个人 Skill 默认私有，经过审核后可以共享给团队或分配给指定 Agent。

### 3. LLM-Wiki

LLM-Wiki 将产品文档、设计说明、运维手册等资料整理为结构化页面，并建立页面之间的链接关系。

Agent 可以先定位相关主题，再按需深入阅读关联内容，而不必每次从头扫描所有文档。

### 4. CodeGraph

CodeGraph 对代码仓库建立结构化索引，内容包括：

- 文件；
- 代码符号；
- 调用方与被调用方；
- 调用关系；
- 改动影响路径。

它不仅告诉 Agent“代码在哪里”，还帮助 Agent 判断“修改这里可能影响哪些地方”。

## 四、系统组成

从仓库结构和部署说明来看，项目主要包含以下模块：

| 模块 | 主要职责 |
| --- | --- |
| MemoryCore | 对话记忆的保存、提炼、分层和检索 |
| MemoryKnowledge | Wiki、CodeGraph 等知识资产的构建与查询 |
| MemoryPanel / Memory Hub | 团队、Agent、记忆资产和权限的 Web 管理界面 |
| MemoryProxy | 位于 Agent 与模型 API 之间，负责协议适配和记忆注入 |
| SDK | 为其他应用提供记忆核心接入能力 |
| deploy | Docker 镜像和一键部署脚本 |

典型工作流程如下：

```text
Claude Code / Codex / OpenClaw / 其他 Agent
                    │
                    ▼
              Memory Proxy
                    │
        ┌───────────┴───────────┐
        ▼                       ▼
     模型 API              Memory Hub / Core
                                │
                  ┌─────────────┼─────────────┐
                  ▼             ▼             ▼
             Chat Memory      Wiki/Skill    CodeGraph
```

Agent 可以将模型 API 的 Base URL 指向 Memory Proxy。Proxy 在请求过程中与 Memory Hub、Memory Core 协作，获取当前 Agent 有权使用的记忆资产。

文档和代码不会被一次性全部放入上下文。Agent 可以先通过 `/v3/tools/list` 发现可用能力，再通过 `/v3/tools/call` 按需读取 Wiki 页面、源代码或影响路径。

## 五、团队协作与权限管理

Chat Memory、Skill、Wiki 和 CodeGraph 会被统一登记为 Memory Asset。平台可以管理资产的：

- Owner；
- 版本；
- 状态；
- 可见范围；
- 使用次数；
- Agent 绑定关系。

项目提供以下可见性级别：

| 可见性 | 含义 |
| --- | --- |
| `private` | 只有 Owner 可以读取，团队管理员也不能直接查看 |
| `team` | 团队成员可读，Owner 或团队管理员可以管理 |
| `restricted` | 通过 User、Role 或 Agent ACL 进行精确授权 |
| `agent` | 定向装备给同一团队中的指定 Agent |

例如，可以为不同角色配置不同的记忆资产：

- 研究 Agent：市场研究 Wiki、用户访谈 Chat Memory、竞品分析 Skill；
- 开发 Agent：产品 Wiki、项目 CodeGraph、功能交付 Skill；
- 审查 Agent：历史事故记忆、项目 CodeGraph、发布检查 Skill。

这种设计更像“给 Agent 配置装备”，而不是将所有知识无差别地提供给所有 Agent。

## 六、与普通 RAG 的区别

普通 RAG 主要回答“可以检索到什么”，TencentDB Agent Memory 还需要回答：

- 这条信息属于谁；
- 哪个版本当前有效；
- 哪些用户或 Agent 有权使用；
- 应该把哪些资产分配给哪个 Agent；
- 修改某段代码会影响哪些调用路径。

| 能力 | 聊天记录 | 普通 RAG | TencentDB Agent Memory |
| --- | --- | --- | --- |
| 跨会话理解用户 | 有限 | 有限 | Chat Memory 分层记忆 |
| 复用可执行经验 | 不支持 | 通常不支持 | 版本化 Skill |
| 文档关系 | 不支持 | 以切片检索为主 | Wiki + Link Graph |
| 代码影响分析 | 不支持 | 以文本匹配为主 | CodeGraph 调用关系 |
| 资产治理 | 不支持 | 能力不统一 | Owner、版本、状态和用量管理 |
| 团队与 Agent 分配 | 不支持 | 通常不支持 | 团队共享、ACL 和固定绑定 |

因此，这个项目更接近一个 Agent Memory Hub 或“记忆资产控制面板”，而不是单独的向量数据库。

## 七、安装方式

官方提供 Docker 一键部署，可以同时启动 `memory-core`、`memory-hub` 和 `proxy`：

```bash
git clone https://github.com/Tencent/TencentDB-Agent-Memory.git
cd TencentDB-Agent-Memory/deploy/global-images
cp .env.example .env
$EDITOR .env
./start-all.sh
```

需要在 `.env` 文件中配置两组模型参数：

- **memory 组**：用于记忆提炼、加工和检索；
- **proxy 组**：用于 Agent 实际调用模型。

启动完成后，可以通过以下地址打开管理面板：

```text
http://localhost:8125
```

随后可以在 Panel 中创建团队和 Agent，导入对话、文档或代码仓库，并为不同 Agent 绑定所需的记忆资产。

## 八、支持的 Agent

README 当前列出的接入对象包括：

- Claude Code；
- Codex；
- CodeBuddy；
- WorkBuddy；
- DeepSeek Harness；
- Hermes；
- OpenClaw。

项目主打通过 Proxy 修改模型 API 地址进行接入，协议保持不变。对于部分 Agent，无须额外安装插件、Hook 或 MCP Server。

其他能够配置模型 API 地址的 Agent，也可以尝试按照项目提供的通用方式接入。

## 九、适用场景

该项目比较适合以下情况：

- 长期使用多个编码、研究或办公 Agent；
- 需要在不同会话中持续保留用户偏好和项目决策；
- 项目文档和代码规模较大，新 Agent 冷启动成本较高；
- 希望把排障、评审、交付和发布流程沉淀为 Skill；
- 由研究、开发、测试、发布等不同角色 Agent 组成协作流程；
- 需要区分私人记忆、团队共享资产和细粒度 ACL；
- 希望 Agent 在修改代码前了解符号和调用关系。

以下场景可能不需要部署完整系统：

- 一次性的简单问答；
- 文档规模很小，只需要基础语义检索；
- 不涉及团队、权限、版本或多 Agent 分配；
- 当前无法承担额外服务、模型调用和资产维护成本。

## 十、当前限制与注意事项

根据项目 README：

- 当前版本标注为 `v2.0.0`，Team Memory 仍处于快速迭代阶段；
- Wiki 和 CodeGraph 采用异步构建，导入后需要等待资产达到 `ready` 状态；
- CodeGraph 当前优先支持公开 HTTPS 仓库；
- 私有仓库和 SSH 凭据支持仍在完善；
- 平台支持手动绑定记忆资产，但完全自动化的记忆路由仍在迭代；
- 项目公布的 PersonaMem 结果由 48% 提升至 76%，该结果适合作为项目方的初步基准，实际使用前仍应针对自身场景进行测试。

在生产环境部署前，还应重点评估：

- 对话、文档与代码的存储边界；
- 模型服务商的数据处理策略；
- API 密钥与数据库凭据保护；
- 用户、团队和 Agent 权限配置；
- 数据备份、恢复和旧版本迁移；
- 记忆提炼质量、召回准确率、延迟与模型费用。

## 十一、总结

TencentDB Agent Memory 的核心价值，不只是保存 Agent 的历史对话，而是把长期记忆、工作技能、文档知识和代码关系统一整理为可治理的资产，并通过团队、Owner、版本、ACL 和 Agent 绑定机制实现安全共享。

如果目标只是为单个聊天机器人增加简单的历史搜索功能，这套系统可能偏重；但对于长期、多项目、多 Agent，并且重视知识复用与权限边界的工作环境，它提供了一个较完整的开源方案。

建议先选择一个非敏感项目进行小规模试点，观察记忆提炼质量、检索效果、响应延迟、模型费用和维护成本，再决定是否扩大部署。

## 参考资料

- [项目主页](https://github.com/TencentCloud/TencentDB-Agent-Memory)
- [中文 README](https://github.com/TencentCloud/TencentDB-Agent-Memory/blob/feat/server_team/README_CN.md)
- [中文安装文档](https://github.com/TencentCloud/TencentDB-Agent-Memory/blob/feat/server_team/INSTALL_CN.md)
- [项目路线图](https://github.com/TencentCloud/TencentDB-Agent-Memory/blob/feat/server_team/ROADMAP_CN.md)

