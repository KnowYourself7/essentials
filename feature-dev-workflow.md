---
name: feature-dev-workflow
description: claude-blog-v2 每个二次开发新功能的固定流程：先判断必要性 → 讨论定方案 → 开分支实现 → 提交 → 自检 → 开 PR → 合并
metadata:
  node_type: memory
  type: feedback
  originSessionId: 3b9ae2d5-448a-4b6e-a3e8-e71efef93be8
  modified: 2026-10-17T09:35:45.643Z
---

用户在 claude-blog-v2（fork）上提出的每一个二次开发新功能，都按下面顺序推进，不跳步：

1. **判断必要性**：先回答"这个功能真的需要吗？"。原则是"如无必要，勿增实体"：现有功能或配置已能解决、收益抵不过维护成本、只是锦上添花的，直接说明理由并建议不做，等用户决定。
2. **讨论方案**：确认需要后，给出 1–2 个方案并明确推荐一个，和用户讨论到方案定下来。方案没确认前不写代码。
3. **开分支实现**：从最新的 main 开新分支，按规范命名（如 `feat/xxx`、`fix/xxx`、`docs/xxx`）。只有用户明确要求时才直接在 main 上改。
4. **提交**：小步、原子提交，提交信息用 Conventional Commits 格式。
5. **自检**：跑项目规定的检查（`python3 -m pytest tests/`、`python3 scripts/lint_prose.py` 等），再自己过一遍 diff；有失败如实报告，不掩盖。
6. **开 PR**：PR 描述写清楚改了什么、为什么改、怎么验证的。
7. **合并**：检查通过后合并，同步本地 main，并在 `docs/FORK-DEVLOG.md` 记一条。

**Why:** 用户是业余程序员，想通过完整、专业的流程打基本功；同时不想让 fork 里堆积没必要的功能。

**How to apply:** 用户一提新功能想法，第一步给必要性判断，而不是直接动手；每完成一步，用一句话说明做了什么、为什么。建 venv 时注意 [[uv-python-314-pin]]。
