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
- Operational walkthroughs when the user asks how to perform a technical process and wants low-reading-cost instructions

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

## Content-Type Mapping

Choose the output form by information type. Do not force every answer into a table.

| 内容类型 | 默认写法 | 不要默认用 |
|---|---|---|
| 差异对比 / 支持度判断 | table | 长段自然段 |
| 阶段完成定义 / DoD | checklist | 宽表格 |
| 接口验收条件 / Acceptance Criteria | 条目式 criteria 或 Gherkin | 宽表格 |
| 执行链路 / Control Flow | 树形箭头 / flow summary | 宽表格 |
| 支持矩阵 / Capability Matrix | table | 长句清单 |

Apply these norms:

- `Definition of Done`: answer with a checklist that says whether a phase is done.
- `Acceptance Criteria`: answer with itemized pass conditions, or Gherkin if the user wants test-style wording.
- `Control Flow / Execution Flow`: answer with a tree-style flow or flow summary that shows how the path runs.
- `Capability Matrix`: answer with a compact table that shows support, impact, or ownership.

## Style Rules

- State the conclusion first. Do not start with how the analysis was done.
- Do not start with a large natural-language paragraph.
- Prefer tables when comparing files, APIs, structs, behaviors, or implementations.
- Do not use a table when the content is naturally a checklist, acceptance criteria list, or control flow.
- Keep sentences short and hard. One sentence should carry one point.
- Keep only information directly relevant to the question.
- Omit background theory unless the user explicitly asks for it.
- Use bold only for section labels or very short conclusions.
- Prefer monospace for file names, symbols, structs, enums, functions, and paths.
- If a table already carries the comparison, do not restate every row in prose.
- Keep the close-out to one sentence unless the user explicitly asks for next steps.
- If the user asks `怎么操作`, `怎么安装`, `怎么执行`, `怎么走流程`, or similar procedural questions, prefer a flow-summary answer in indented tree form rather than long prose.
- If the user asks for `完成标准`, `验收标准`, or `Definition of Done`, default to checklist or criteria form before considering a table.
- If the user asks for `流程`, `链路`, `闭环`, or `怎么走`, default to tree-style flow before considering a table.

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
- Keep the first column narrow.
- Do not put too many `/`-joined items into one first-column cell.
- If the first column becomes wider than the rest of the table, split the row into smaller groups.
- Prefer 1-3 items per first-column cell.
- If a retriever or player capability group is too long, split it into multiple rows rather than forcing a single line.

## Flow Summary Pattern

When the user asks for an operational flow or installation process, prefer a compact summary flow that matches this style:

```text
请求到达 -> step_a() -> waiting 队列
                     |
                     v
module.run()
├─ phase_1: prepare() -> 校验输入 -> 命中/未命中分支
│  ├─ 命中: reuse_state -> 更新计数
│  └─ 未命中: create_state -> 分配资源
├─ phase_2: execute() -> 主逻辑处理
└─ finish: cleanup() -> 回收资源 -> 返回结果
```

Use this pattern instead of box-style ASCII flowcharts.

Use this pattern instead of sequence diagrams unless actor interaction is the whole point of the answer.

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
- If a table row is too wide, split the first column group before shortening the explanation column

## What to Avoid

- Long reasoning chains
- Repeating the same fact in prose after a table
- Large bullet trees
- “I checked / I found / I think” framing
- Historical or architectural context that does not change the answer
- Long methodology explanations
- Pure text-only step dumps when a compact tree-style flow summary would make the process easier to scan
- Overwide first-column cells that make the judgment or explanation columns unreadable

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

### Template E: Operational Walkthrough

Use when the user asks how to perform a process, install something, or execute a workflow.

```markdown
**操作流程**

```text
请求开始 -> step_1() -> 中间状态
                     |
                     v
main_flow()
├─ phase_1: ...
├─ phase_2: ...
└─ finish: ... -> 完成
```

**具体操作**

| 步骤 | 操作 |
|---|---|
| 1 | ... |
| 2 | ... |

**结果**

一句话说明做完后会看到什么。
```

### Template F: Definition of Done

Use for phase completion, milestone closure, or "done means what".

```markdown
**Phase X Definition of Done**

- [ ] ...
- [ ] ...
- [ ] ...
```

### Template G: Acceptance Criteria

Use for interface-level or feature-level pass conditions.

```markdown
**`xxx` Acceptance Criteria**

- 前置条件 ...
- 调用后返回成功
- 状态变为 ...
- 能观察到 ...
```

If the user wants test wording, use Gherkin:

```gherkin
Feature: xxx

Scenario: yyy
  Given ...
  When ...
  Then ...
```

### Template H: Control Flow / Capability Closure

Use for execution flow, capability closure, chain walkthrough, or "how the path runs".

```markdown
**`xxx` Flow**

```text
Module
└─ phase_a
   -> step_1
   -> step_2
   -> step_3
```
```

### Template I: Capability Matrix

Use for support / impact / compatibility / ownership matrices.

```markdown
**能力矩阵**

| 能力 | 是否受影响 | 说明 |
|---|---|---|
| `foo` | 否 | ... |
| `bar` | 中 | ... |
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
- If the follow-up is procedural, switch to flow-summary-first output.
