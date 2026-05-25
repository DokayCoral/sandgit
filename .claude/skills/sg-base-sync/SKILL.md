---
name: sg-base-sync
description: 切换到 main 分支并初始化干净工作环境，远程同步为可选步骤，本地优先，用于沙箱开发流程的第一步
license: MIT
compatibility: Requires Git CLI.
metadata:
  author: sandgit
  version: "2.0"
---

# Skill Name: sg-base-sync

## Skill Purpose
切换到本地 main 分支，清理工作环境，为后续沙箱操作提供干净基线。远程同步为可选项，默认以本地 main 为权威基线，完全支持纯本地 Git 使用。

## Use This Skill When
1. 用户要求同步主干基线，准备开始新沙箱开发流程
2. 需要从任意分支切回 main 并清理工作环境
3. 工作目录存在残留变更，需要确认保存或丢弃后切回主干
4. 新项目初始化，需要确认 main 为工作基线
5. 用户明确要求同步远程最新 main 代码

## Do NOT Use This Skill When
1. 用户正处于沙箱分支开发中且不希望被打断
2. 仓库处于 rebase/merge 冲突中间状态
3. 需要合并其他非 main 分支的内容
4. 用户只想查看分支状态而不希望实际切换

## Input Params
无必填参数。远程同步由技能主动询问用户决定。

## Execution Steps
1. 执行前置环境校验：确认当前目录为 Git 仓库根目录，确认 Git CLI 可用
2. 检测当前分支和未提交变更：
   - 执行 `git branch --show-current` 获取当前分支名
   - 执行 `git status --porcelain` 检查工作目录是否干净
3. 若当前分支非 main 且有未提交变更（modified/staged/untracked）：
   - 逐项列出受影响文件
   - 使用 AskUserQuestion 询问用户是否保存变更
   - 若用户选择保存：执行 `git add -A && git commit -m "sg-base-sync: auto save before switch"`
   - 若用户拒绝保存：终止执行，保持在当前分支
4. 若所有变更已提交（工作目录干净），直接执行 `git checkout main`
5. 若当前已在 main 分支，跳过切换步骤
6. 检测远程仓库并询问同步：
   - 执行 `git remote get-url origin` 检测 origin 是否存在
   - 若不存在：输出提示"无远程仓库，使用本地 main 作为基线"，跳转到步骤 8
   - 若存在：使用 AskUserQuestion 询问用户"是否同步远程 origin/main 最新代码？"
   - 若用户选择同步：执行步骤 7
   - 若用户拒绝：输出提示"跳过远程同步，使用本地 main"，跳转到步骤 8
7. 拉取远程最新代码：
   - 执行 `git fetch origin main` 获取远程最新提交
   - 比较本地与远程差异：`git rev-list --left-right --count main...origin/main`
   - 若本地落后于远程：执行 `git pull --ff-only origin main`
   - 若已同步：输出"本地 main 已是最新"
   - 若本地领先：提示有未推送提交
   - 若本地与远程分叉：终止执行，输出 `git log --oneline main...origin/main` 供排查
8. 执行结果自检校验：
   - 确认当前分支为 main
   - 确认工作目录干净
   - 执行 `git log -1 --oneline` 输出最新提交信息
9. 输出环境就绪确认摘要：当前 HEAD 提交哈希、分支名称、同步状态

## On Failure Handling
1. 非 Git 仓库：提示"当前目录不是 Git 仓库，请在项目根目录执行"
2. 分支切换失败：输出 `git status` 诊断信息，提示可能原因
3. `git fetch` 网络错误：提示检查网络连接，本地状态不变，继续以本地 main 为基线
4. main 分支不存在：提示"本地不存在 main 分支，请先初始化项目"
5. `git pull --ff-only` 失败（分叉）：输出完整差异日志供排查，终止执行不强制合并
