## Why

任何沙箱开发流程的第一步都是确保本地仓库与主干保持同步。sandgit-base-sync 作为 6 个技能的入口，负责同步主干基线并初始化干净的工作环境，为后续测试沙箱和业务沙箱的创建提供可靠基准。缺少此技能将导致沙箱分支从过时或不一致的基础代码出发，增加合并冲突风险。

## What Changes

- 新增 `.claude/skills/sandgit-base-sync/SKILL.md` 技能文件，实现主干代码同步功能
- 技能执行流程：检查当前分支状态 → 切换到 main → 拉取最新代码 → 清理本地残留
- 同步前自动检测未提交变更并提示处理，避免用户丢失工作进度
- 输出同步状态摘要，确认环境已就绪

## Capabilities

### New Capabilities
- `sandgit-base-sync`: 同步主干基线并初始化干净工作环境，为后续沙箱操作提供最新基准代码

### Modified Capabilities
<!-- 无现有 capability 需要修改 -->

## Impact

- 新增文件: `.claude/skills/sandgit-base-sync/SKILL.md`
- 关联文件: `sandgit-rules.md`（如需引用全局规则）
- 无 API、外部依赖影响
