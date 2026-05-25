## 1. Rename skill

- [x] 1.1 Delete old skill directory `.claude/skills/sandgit-base-sync/`
- [x] 1.2 Create new skill directory `.claude/skills/sg-base-sync/`

## 2. Rewrite SKILL.md

- [x] 2.1 Write frontmatter with new name `sg-base-sync` and updated description (emphasize local-first)
- [x] 2.2 Write "Skill Purpose": 切换到 main 分支并初始化干净环境，远程同步为可选步骤
- [x] 2.3 Write "Use This Skill When" section: 本地为主，远程可选
- [x] 2.4 Write "Do NOT Use This Skill When" section
- [x] 2.5 Write "Input Params": 无必填参数
- [x] 2.6 Write "Execution Steps" (refactored logic per spec):
  - Step: Preflight check (git repo root, git CLI)
  - Step: Detect current branch and uncommitted changes
  - Step: If uncommitted changes → list files, ask user to save (commit) or abort
  - Step: If committed/clean → switch to main directly
  - Step: Detect remote → if exists, ask user whether to sync; if no remote, skip
  - Step: If user wants sync → fetch + pull --ff-only; handle divergence by aborting
  - Step: Output summary (branch, HEAD, sync status)
- [x] 2.7 Write "On Failure Handling" section

## 3. Update README.md

- [x] 3.1 Update "项目目录说明" section: `sandgit-base-sync` → `sg-base-sync` (section no longer exists in rewritten README; skill names already aligned)

## 4. Validation

- [x] 4.1 Verify SKILL.md conforms to skills-writing-rules.md
- [x] 4.2 Verify all spec scenarios covered: clean switch, uncommitted save, uncommitted abort, no remote, remote sync, remote decline, sync divergence
