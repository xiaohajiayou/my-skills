---
name: structured-code-analysis
description: Present code review findings, header or API comparisons, implementation differences, and compatibility judgments in a conclusion-first, highly scannable format. Use when the user asks to compare code, judge whether interfaces or implementations match, assess impact on design or implementation, or wants low-reading-cost technical answers with short verdicts, compact tables, and minimal background explanation.
---

# Structured Code Analysis

Write analysis answers in a fixed, terse structure. Optimize for scan speed, not narrative flow.

## Output Order

Use this order unless the user asks for something narrower:

1. Title line or short section header
2. One-line conclusion
3. Compact comparison table or checklist
4. Critical difference or key risk
5. Impact
6. Optional next step in one line

## Style Rules

- State the conclusion first. Do not start with how the analysis was done.
- Prefer tables over long prose when comparing files, APIs, structs, behaviors, or implementations.
- Keep sentences short and hard. One sentence should carry one point.
- Keep only information directly relevant to the question.
- Omit background theory unless the user explicitly asks for it.
- Use bold only for section labels or very short conclusions.
- Prefer monospace for file names, symbols, structs, enums, functions, and paths.

## Table Pattern

Use a 2-column table by default:

| Item | Difference |
|---|---|
| `file_a.h` | Only comment encoding differs |
| `file_b.h` | In-tree adds fields; Linux keeps public form |

Use a 3-column table only when the user needs explicit side-by-side mapping:

| Item | Linux | in-tree |
|---|---|---|
| event | absent | `FOO_EVENT` |

## Section Pattern

Use short sections like these:

- `结论`
- `对比结果`
- `致命差异`
- `影响`

Do not add extra sections unless they reduce reading cost.

## What to Avoid

- Long reasoning chains
- Repeating the same fact in prose after a table
- Large bullet trees
- “I checked / I found / I think” framing
- Historical or architectural context that does not change the answer

## Good Answer Shape

```markdown
**某模块对比结果**

**公共 API —— 不完全一致**

| 头文件 | 差异 |
|---|---|
| `a.h` | 仅注释编码差异 |
| `b.h` | 多 2 个字段，少 1 个事件 |

**致命差异**

Linux 版只有 public 头文件；in-tree 依赖内部接口。

**影响**

标准链路不受影响。扩展注入路径风险高。
```

## When Answering Follow-up Questions

- Keep the same structure.
- Narrow the table to only the object the user asked about.
- If only one file or one symbol is in scope, replace the big table with a tiny table.
- If the answer is just one correction, use one conclusion line plus one table.
