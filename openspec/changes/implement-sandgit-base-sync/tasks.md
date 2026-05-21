## 1. Setup

- [x] 1.1 Create `.claude/skills/sandgit-base-sync/` directory and `SKILL.md` file (if not already present)

## 2. Write SKILL.md following skills-writing-rules.md

- [x] 2.1 Write frontmatter metadata (name, description, license, metadata) referencing openspec-propose SKILL.md as format example
- [x] 2.2 Write "Skill Purpose" section: 同步主干基线，初始化干净环境
- [x] 2.3 Write "Use This Skill When" section: 正向触发场景（开始新沙箱流程前、需要同步主干基线时、本地环境混乱需要重置时）
- [x] 2.4 Write "Do NOT Use This Skill When" section: 反向禁用场景（正在沙箱开发中、有未保存工作且不可中断、非 main 分支的合并操作）
- [x] 2.5 Write "Input Params" section: 可选参数（远程名称默认 origin）
- [x] 2.6 Write "Execution Steps" section covering all spec requirements:
  - Step: Detect current branch and uncommitted changes (spec Requirement 1)
  - Step: Stash uncommitted changes if any, with user confirmation
  - Step: Switch to main branch
  - Step: Detect remote presence (no-remote → skip pull, use local main as baseline)
  - Step: Pull latest from origin/main with --ff-only
  - Step: Handle divergent branches by aborting
  - Step: Output sync summary with HEAD commit hash + environment readiness confirmation
- [x] 2.7 Write "On Failure Handling" section covering: 路径错误、命令执行失败、环境缺失

## 3. Validation

- [x] 3.1 Verify SKILL.md conforms to skills-writing-rules.md (六大结构、命名规范、无禁止反模式)
- [x] 3.2 Dry-run verify: skill logic handles all spec scenarios (clean main, dirty worktree, non-main branch, no remote, fast-forward, divergent)
