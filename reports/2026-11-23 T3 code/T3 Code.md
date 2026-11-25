# T3 Code 功能介绍

T3 Code 是一个开源应用，可以在同一个界面里用 Codex、Claude Code 等 AI 编程助手。你可以把不同的任务交给不同的助手，再回到同一个地方看进度和结果。读代码、改文件和跑测试由各自的助手负责，用量也记在各自的账号上。 [项目介绍](https://github.com/pingdotgg/t3code#readme)

## 同时看几件事的进度

比如，CSV 文件末尾多一行空白，导入时就报错。可以让 Codex 修这个问题，同时让 Claude Code 看看现有测试漏了哪些情况。两件事各有一条对话，从侧栏就能看出哪条还在跑、哪条做完了、哪条在等你确认。检查完结果的对话可以从当前任务列表里收起来，以后还能翻看记录。 [会话管理](https://github.com/pingdotgg/t3code/blob/main/docs/user/thread-sidebar.md)

## 把一条对话里的发现交给另一条

如果 Claude Code 发现“文件只有表头”这种情况还没测过，你可以在 Codex 的修复对话里引用那条检查记录，让 Codex 参考这个发现，判断要不要补一个测试。这样两边可以先各做各的，需要时再把结果接起来。 [会话引用](https://github.com/pingdotgg/t3code/blob/main/docs/user/composer.md#context-in-your-message)

## 离开电脑后，用手机接着处理

在电脑上交代的任务，到了手机上还能打开同一条对话，查看进度或继续回复。如果助手在等你批准运行测试，你也可以在手机上确认，测试会在原来的电脑上继续跑。远程处理时，工作电脑需要保持开机，手机也要能连到上面的 T3 Code。 [远程访问](https://github.com/pingdotgg/t3code/blob/main/docs/user/remote-access.md)

## 分开改代码，逐行看结果

如果两件事都要改同一个 Git 项目，T3 Code 可以给每个任务分一个独立的工作目录（Git worktree），让两边同时改代码，避免互相覆盖。任务做完后，改动面板会列出改过的文件，逐行显示加了什么、删了什么。比如修 CSV 导入时，你可以对照改动和测试结果，看空行能不能跳过，以及遇到有数据但缺字段的行时会不会照常报错。 [项目的 Git 功能](https://github.com/pingdotgg/t3code#what-you-get) · [查看改动](https://github.com/pingdotgg/t3code/blob/main/docs/user/source-control.md#review-turn-changes)
