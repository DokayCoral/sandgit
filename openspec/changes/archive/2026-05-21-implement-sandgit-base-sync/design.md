## Context

sandgit 是一套基于 Git 原生分支隔离能力的轻量沙箱开发规范。`sandgit-base-sync` 是整个工作流的入口技能，负责在每次沙箱操作前将本地仓库与远程主干同步。技能形式为 `.claude/skills/sandgit-base-sync/SKILL.md`（文件夹 + SKILL.md），遵循项目 `skills-writing-rules.md` 规范编写。

约束条件：
- 仅依赖原生 Git 命令，零外部工具
- 兼容 Windows / macOS / Linux
- 遵循 skills-writing-rules.md 的六大固定结构（名称用途 → 正向场景 → 反向场景 → 输入参数 → 执行步骤 → 异常兜底）

## Goals / Non-Goals

**Goals:**
- 将本地 main 分支与远程 origin/main 保持同步
- 确保工作目录处于干净状态（无未提交变更、无残留 stash）
- 输出清晰的状态摘要，让用户确认环境就绪
- 对潜在数据风险（未提交变更、未推送提交）主动警告并交由用户决策

**Non-Goals:**
- 不创建任何沙箱分支（由 sandgit-test-branch-run / sandgit-code-branch-run 负责）
- 不执行代码审查或测试
- 不处理 main 以外的分支同步

## Decisions

1. **技能形式**：Claude Code Skill（文件夹 + SKILL.md），由 AI 逐步执行 Git 命令。
   - 理由：sandgit 为 AI 辅助编程设计，AI 直接执行 Git 操作更灵活，能根据上下文做智能决策（如检测到 stash 冲突时给出建议），而非机械执行脚本。
   - 替代方案（独立脚本）：执行速度快但失去上下文感知能力。

2. **执行步骤**：检查当前分支 → 处理未提交变更（stash） → 切换到 main → 拉取最新代码 → 输出摘要。
   - 理由：每步有明确的成功/失败判断标准，出错可精确定位。stash 保护用户未提交工作而非直接丢弃。
   - 替代方案（`checkout -f main`）：简单但会丢失用户未提交变更，风险过高。

3. **远程名称**：固定使用 `origin`。
   - 理由：覆盖 99% 使用场景，避免参数复杂度。如有特殊需求后续可扩展为输入参数。

4. **无远程仓库处理**：检测到无 remote 时，跳过 pull 步骤，将本地 main 作为权威基线。
   - 理由：sandgit 定位为轻量规范，允许纯本地使用场景（如新建项目尚未关联远程）。不做强制远程校验。

## Risks / Trade-offs

- **风险**：用户有未 commit 变更，stash 后忘记恢复 → **缓解**：执行前清晰列出变更文件，stash 后输出恢复命令 `git stash pop`
- **风险**：网络原因 pull 失败 → **缓解**：检测失败后提示用户检查网络，本地状态保持不变
- **风险**：仓库无远程配置 → **缓解**：预先检测 remote，无远程时跳过 pull，以本地 main 为基线
- **权衡**：Skill 形式 vs 脚本 → Skill 形式输出上下文相关建议，但执行需分步确认
