## 1. Setup

- [x] 1.1 Create `.claude/skills/sg-test-branch-run/` directory and `SKILL.md` file

## 2. Write SKILL.md following skills-writing-rules.md

- [x] 2.1 Write frontmatter metadata (name: sg-test-branch-run, description, license, metadata)
- [x] 2.2 Write "Skill Purpose": 从 main 创建测试沙箱分支，辅助编写测试，引导结构化三步审核
- [x] 2.3 Write "Use This Skill When" section: 开始测试编写、需要创建测试沙箱、需要在隔离环境调试测试
- [x] 2.4 Write "Do NOT Use This Skill When" section: 未执行 sg-base-sync、已在沙箱中、不需要测试的项目
- [x] 2.5 Write "Input Params": 分支名称后缀（用户输入，必填）
- [x] 2.6 Write "Execution Steps" covering all spec requirements:
  - Step: Preflight check (git repo, must be on main)
  - Step: Create `sgtest/<name>` from main, handle name conflict
  - Step: Assist test writing (free-form, full read/write permissions)
  - Step: Review item ① — test coverage description (WHAT), confirm or revise
  - Step: Review item ② — test methodology description (HOW), confirm or revise
  - Step: Review item ③ — static case verification (EVIDENCE), confirm or fix & re-run
  - Step: Output one test document (coverage + methodology + verification), commit to sandbox
  - Step: Prompt for sg-test-finalize
- [x] 2.7 Write "On Failure Handling" section: 不在 main、分支名非法、创建失败、静态用例失败、任何阶段放弃

## 3. Validation

- [x] 3.1 Verify SKILL.md conforms to skills-writing-rules.md (六大结构、命名规范、无禁止反模式)
- [x] 3.2 Verify all spec scenarios covered: valid name, existing name, not on main, test writing, 3 review items (confirm/revise), static cases (pass/fail), discard
