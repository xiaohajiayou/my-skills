---
name: structured-code-analysis
description: Present code review findings, header or API comparisons, implementation differences, compatibility judgments, and design impact assessments in a conclusion-first, highly scannable format. Use when the user asks to compare code, validate whether technical conclusions are correct, judge whether interfaces or implementations match, or wants low-reading-cost technical answers with short verdicts, compact tables, and minimal background explanation.
---

# Structured Code Analysis

Write analysis answers in a fixed, terse structure. Optimize for scan speed, not narrative flow.

## Scope

Use this skill for:

- Code review findings
- Header and API comparisons
- Implementation difference analysis
- Compatibility judgments
- Design impact assessment
- Correcting or validating another technical conclusion

Do not use this skill for:

- General brainstorming
- Teaching-oriented long explanations
- Casual discussion
- Pure implementation tasks where the user wants code changes rather than analysis output

## Output Order

Use this order unless the user asks for something narrower:

1. Title line or short section header
2. One-line conclusion
3. Compact comparison table or checklist
4. Critical difference or key risk
5. Impact
6. Optional next step in one line

Prefer at most 4 sections in the final answer.

## Style Rules

- State the conclusion first. Do not start with how the analysis was done.
- Default to table-first output. Do not start with a large natural-language paragraph.
- Prefer tables over long prose when comparing files, APIs, structs, behaviors, or implementations.
- Keep sentences short and hard. One sentence should carry one point.
- Keep only information directly relevant to the question.
- Omit background theory unless the user explicitly asks for it.
- Use bold only for section labels or very short conclusions.
- Prefer monospace for file names, symbols, structs, enums, functions, and paths.
- If a table already carries the comparison, do not restate every row in prose.
- Keep the close-out to one sentence unless the user explicitly asks for next steps.

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

Preferred table headers:

- `| 头文件 | 差异 |`
- `| 项目 | 差异 |`
- `| 能力 | 是否受影响 | 说明 |`
- `| 对象 | 判断 |`

## Section Pattern

Use short sections like these:

- `结论`
- `对比结果`
- `致命差异`
- `关键差异`
- `影响`
- `修正版`

Do not add extra sections unless they reduce reading cost.

## Compression Rules

- Default to 2-4 sections total
- Default to 1 short conclusion line before the first table
- Prefer one table plus one short impact block over multiple prose paragraphs
- Keep sections to 3-5 lines unless the user explicitly asks for detail
- If the user asks about one file or one symbol, use a tiny table rather than a broad summary
- If correcting another answer, first say `正确 / 不完全正确 / 不正确`, then give the corrected version

## What to Avoid

- Long reasoning chains
- Repeating the same fact in prose after a table
- Large bullet trees
- “I checked / I found / I think” framing
- Historical or architectural context that does not change the answer
- Long methodology explanations

## Fixed Templates

### Template A: Single Object Difference

Use for one file, one struct, one enum, one symbol, or one API.

```markdown
**`xxx` 对比结果**

**结论**

一句话先说结论。

| 项目 | 差异 |
|---|---|
| `foo` | ... |
| `bar` | ... |

**影响**

- ...
- ...
```

### Template B: Multi-object Comparison

Use for multiple headers, modules, or APIs.

```markdown
**对比结果**

**公共接口 —— 完全一致 / 不完全一致**

| 头文件 | 差异 |
|---|---|
| `a.h` | ... |
| `b.h` | ... |

**致命差异**

一句话指出最大问题。

**影响**

一句或两句说明实现影响。
```

### Template C: Implementation Impact

Use for design impact, migration impact, or implementation feasibility.

```markdown
**实现影响结果**

| 能力 | 是否受影响 | 说明 |
|---|---|---|
| 标准播放链 | 否 | ... |
| Raw ES 注入 | 是 | ... |
| network 扩展事件 | 是 | ... |

**关键影响**

- ...
- ...

**结论**

一句话收束。
```

### Template D: Correcting Another Conclusion

Use when the user pastes another model's or another person's conclusion and asks whether it is correct.

```markdown
**结论**

正确 / 不完全正确 / 不正确。

| 项目 | 判断 |
|---|---|
| `a.h` | 正确 |
| `b.h` | 不准确，原因是 ... |

**修正版**

给出一版可以直接引用的修正表述。
```

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
