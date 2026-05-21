---
name: sandgit-base-sync
description: 同步主干基线，拉取远程 main 分支最新代码并初始化干净工作环境，用于沙箱开发流程的第一步
license: MIT
compatibility: Requires Git CLI.
metadata:
  author: sandgit
  version: "1.0"
---

# Skill Name: sandgit-base-sync

## Skill Purpose
同步本地 main 分支与远程 origin/main 保持一致，清理工作目录残留变更，输出环境就绪确认，为后续沙箱操作提供最新基线代码。

## Use This Skill When
1. 用户明确要求同步主干基线、拉取最新 main 分支代码
2. 开始新的沙箱开发流程前，需要确保本地环境与远程同步
3. 本地 main 分支落后于远程，需要更新到最新提交
4. 工作目录存在残留变更，需要清理并重置到干净状态
5. 新项目首次初始化，需要确认本地 main 为权威基线

## Do NOT Use This Skill When
1. 用户正处于沙箱分支中（sgtest/* 或 sgcode/*）进行开发工作
2. 存在未保存工作且暂不能中断的紧急任务
3. 需要合并其他非 main 分支的内容
4. 用户只想查看远程差异而不希望实际拉取代码
5. 仓库处于 rebase/merge 冲突中间状态

## Input Params
1. remote_name: 字符串 | 选填 | 远程仓库名称，默认值为 `origin`

## Execution Steps
1. 执行前置环境校验：确认当前目录为 Git 仓库根目录，确认 Git CLI 可用
2. 检测当前分支名称和未提交变更：
   - 执行 `git status --porcelain` 检查工作目录是否干净
   - 执行 `git branch --show-current` 获取当前分支名
3. 若有未提交变更（modified/staged/untracked），逐项列出受影响文件，提示用户确认后执行 `git stash push -u -m "sandgit-base-sync auto stash"`
4. 若当前分支非 main，执行 `git checkout main` 切换到 main 分支
5. 检测远程仓库配置：
   - 执行 `git remote get-url origin` 检测 origin 是否存在
   - 若不存在远程仓库：跳过拉取步骤，输出提示"无远程仓库，以本地 main 为基线"，跳转到步骤 7
   - 若存在远程仓库：继续步骤 6
6. 拉取最新代码：
   - 执行 `git fetch origin main` 获取远程最新提交
   - 比较本地与远程差异：`git rev-list --left-right --count main...origin/main`
   - 若本地落后于远程：执行 `git pull --ff-only origin main`
   - 若本地领先于远程：提示用户有未推送提交，用户确认后继续
   - 若本地与远程分叉：终止执行，提示用户手动解决分叉
7. 执行结果自检校验：
   - 确认当前分支为 main
   - 确认工作目录干净（无未提交变更）
   - 执行 `git log -1 --oneline` 输出最新提交信息
8. 输出环境就绪确认摘要：当前 HEAD 提交哈希、分支名称、是否已同步远程

## On Failure Handling
1. 非 Git 仓库：提示"当前目录不是 Git 仓库，请在项目根目录执行"
2. 分支切换失败：提示可能原因（存在未提交变更且 stash 失败），输出 `git status` 诊断信息
3. `git fetch` 网络错误：提示检查网络连接，输出完整错误信息，本地状态保持不变
4. 主分支不存在：提示"本地不存在 main 分支，请先初始化项目或指定正确的主分支名称"
5. `git pull --ff-only` 失败：提示分叉或冲突原因，输出 `git log --oneline main...origin/main` 供用户排查，终止执行不强制合并
