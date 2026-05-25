---
name: sg-test-branch-run
description: 从 main 创建 sgtest 测试沙箱分支，辅助编写测试代码，并通过结构化三步审核（覆盖说明→方法说明→静态用例验证）产出测试文档
license: MIT
compatibility: Requires Git CLI.
metadata:
  author: sandgit
  version: "1.0"
---

# Skill Name: sg-test-branch-run

## Skill Purpose
从 main 创建 `sgtest/<name>` 测试沙箱分支，在隔离环境中辅助用户编写测试代码。完成后执行结构化三步审核（覆盖说明、方法说明、静态用例验证），用户全部确认后产出测试文档，标志沙箱合规。

## Use This Skill When
1. 用户已完成 `sg-base-sync`，当前在 main 分支，准备开始编写测试
2. 需要创建新的测试沙箱，在隔离分支中编写和调试测试代码
3. 测试代码编写完毕，需要进入结构化审核流程确认测试标准正确
4. 审核未通过，需要迭代修改测试代码后重新走审核流程

## Do NOT Use This Skill When
1. 用户尚未执行 `sg-base-sync`，不在 main 分支
2. 用户已处于某个沙箱分支中（sgtest/* 或 sgcode/*）
3. 项目不需要测试即可直接进入业务开发
4. 用户只想合并已审核通过的测试（应使用 `sg-test-finalize`）

## Input Params
1. branch_suffix: 字符串 | 必填 | 测试沙箱分支名称后缀，将自动拼接为 `sgtest/<branch_suffix>`

## Execution Steps
1. 执行前置环境校验：
   - 确认当前目录为 Git 仓库根目录，Git CLI 可用
   - 执行 `git branch --show-current`，若不在 main 分支，提示用户先运行 `sg-base-sync`
2. 创建测试沙箱分支：
   - 使用用户提供的 `branch_suffix` 拼接分支名 `sgtest/<branch_suffix>`
   - 执行 `git branch --list sgtest/<branch_suffix>` 检查分支是否已存在
   - 若已存在：提示用户"该沙箱已存在"，询问是复用已有沙箱还是换名创建
   - 若不存在：执行 `git checkout -b sgtest/<branch_suffix> main` 从 main 创建并切换
3. 辅助编写测试代码：
   - 沙箱内对开发环境内的代码读写权限全开，可自由创建、修改、删除测试文件
   - AI 根据用户需求协助编写测试代码，支持多轮迭代调试
   - 编写完成后询问用户是否进入审核流程
4. 审核项 ① 测试覆盖说明（WHAT）：
   - 整理并展示本次测试覆盖的功能点清单：测试了哪些模块/函数/场景
   - 使用 AskUserQuestion 请用户确认：覆盖范围是否完整、有无遗漏
   - 若用户要求修订：回到修改，补充覆盖说明后重新展示
   - 若用户确认：进入下一项
5. 审核项 ② 测试方法说明（HOW）：
   - 逐项说明每个测试的设计思路和验证方式：输入是什么、预期输出是什么、如何判定通过/失败
   - 使用 AskUserQuestion 请用户确认：测试方法是否正确、验证逻辑是否合理
   - 若用户要求修订：回到修改，调整方法说明后重新展示
   - 若用户确认：进入下一项
6. 审核项 ③ 静态用例验证（EVIDENCE）：
   - 准备静态 mock 数据，运行测试代码，证明测试框架自身逻辑正确
   - 展示运行结果（通过/失败数、关键用例输出）
   - 使用 AskUserQuestion 请用户确认：静态用例结果是否符合预期
   - 若存在失败用例：报告失败详情，允许用户修复测试代码后重新运行
   - 若用户确认：三项审核全部通过
7. 产出测试文档：
   - 将审核通过的 ① 覆盖说明 + ② 方法说明 + ③ 验证结果 汇总为一份测试文档
   - 执行 `git add -A && git commit -m "sg-test-branch-run: test document and test code"` 保存至沙箱分支
   - 输出测试文档摘要，标记沙箱"合规"
8. 提示后续步骤：输出 `sg-test-finalize` 调用提示，告知用户可将测试沙箱合并入 main

## On Failure Handling
1. 不在 main 分支：提示"请先执行 sg-base-sync 切换到 main 分支"，不自动切换
2. 分支名非法：提示合法的分支名格式，要求用户重新输入
3. 分支创建失败：输出完整错误信息，检查是否存在同名分支或 Git 仓库异常
4. 静态用例运行失败：展示失败用例详情，允许用户在沙箱中修复测试代码后重新运行验证
5. 用户在任何审核阶段选择放弃：提示可调用 `sg-branch-abort` 废弃当前沙箱
