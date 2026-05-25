## Why

当前 sandgit-base-sync 技能存在两个问题：(1) 命名与 README.md 不一致，README 已统一为 `sg-*` 前缀；(2) 执行逻辑默认假设有远程仓库，不符合 README "本地优先、有无远程都能用" 的定位。需要对齐命名并重构执行流程，使本地使用场景为默认、远程同步为可选项。

## What Changes

- **BREAKING**: 技能重命名 `sandgit-base-sync` → `sg-base-sync`，技能文件夹及 SKILL.md frontmatter 同步更新
- **BREAKING**: 执行逻辑重写：默认不检测远程仓库，以本地 main 为基线；远程同步改为主动询问用户的可选项
- 非 main 分支处理优化：已提交代码 → 直接切分支；未提交代码 → 询问用户是否保存（commit）后再切
- 更新 `README.md` 项目目录说明中的技能名

## Capabilities

### Modified Capabilities
- `sandgit-base-sync`: 重命名为 `sg-base-sync`，执行逻辑由"默认检测远程"改为"默认本地基线、远程同步可选"

## Impact

- 删除: `.claude/skills/sandgit-base-sync/SKILL.md`（旧技能文件）
- 新增: `.claude/skills/sg-base-sync/SKILL.md`（新技能文件）
- 修改: `README.md`（项目目录说明中的技能名）
