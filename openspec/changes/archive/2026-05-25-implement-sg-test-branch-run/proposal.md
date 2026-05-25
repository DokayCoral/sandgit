## Why

sandgit 工作流的第二步是在独立沙箱中编写测试标准。`sg-test-branch-run` 负责从 main 创建 `sgtest/*` 测试沙箱分支，在隔离环境中辅助用户编写测试代码，并引导人工审核确认。测试标准是后续业务开发的校验基线，必须先固化再推进业务开发。

## What Changes

- 新增 `.claude/skills/sg-test-branch-run/SKILL.md` 技能文件
- 技能执行流程：创建 `sgtest/*` 分支 → 辅助编写测试 → 结构化三步审核（覆盖说明 → 方法说明 → 静态用例验证）→ 产出一份测试文档
- 遵循 README 权限规则：沙箱内对开发环境内的代码读写权限全开，可自由修改测试文件
- 审核通过后交由 `sg-test-finalize` 合并；审核不通过则沙箱内迭代或废弃

## Capabilities

### New Capabilities
- `sg-test-branch-run`: 从 main 创建 `sgtest/*` 测试沙箱分支，辅助编写测试代码，引导人工审核确认测试标准

### Modified Capabilities
<!-- 无现有 capability 需要修改 -->

## Impact

- 新增文件: `.claude/skills/sg-test-branch-run/SKILL.md`
- 关联技能: `sg-base-sync`（前置步骤）、`sg-test-finalize`（后续步骤）
- 无 API、外部依赖影响
