---
name: source-callchain-research
description: Read source code, explain implementation structure, trace execution paths, and guide users through call chains across Python, C/C++, mixed-language projects, async flows, callbacks, threads, or processes. Use when the user asks how code is implemented, how modules cooperate, how a function is reached, where execution goes next, or wants step-by-step source-reading help with low reading cost.
---

# Source Callchain Research

Guide the user through the codebase with high information density. Prefer source-reading guidance over exhaustive analysis.

## Core Goal

Teach the core idea quickly.

- Answer the most essential question directly.
- Show the layer needed for the current question.
- Use code as the main content and prose as glue.
- Keep density high without turning the answer into a dump.

## Default Mode

Default to a direct explanation of the core mechanism, not a full report.

- Answer the main implementation idea in the first reply.
- Do not artificially hold back the key mechanism just to stay high-level.
- Still avoid full end-to-end expansion unless the user asks for the full chain or it is required for correctness.
- If the topic is broad, compress around the central mechanism instead of spreading across many side details.

## Required Opening Structure

When the question spans multiple modules, files, or layers, the first screen must contain:

1. One subject sentence that frames the whole system
2. One short tree-style architecture or flow sketch
3. Then the breakdown

The subject sentence must say:

- how many layers or roles matter here
- what each layer is responsible for
- what data structure or interface connects them

Use this pattern:

```text
完整架构是 X 层配合：
- A 管 ...
- B 管 ...
- 它们通过 C 耦合
```

If that sentence is missing, do not jump into code blocks yet.

## Output Layers

Choose one of these depths and stay there unless the user pushes deeper.

### Layer 1: Core-mechanism summary

Use for broad `怎么实现`, `整体怎么走`, `先带我看一下`.

Allowed content:

- one subject sentence
- one short architecture tree
- 2-4 concept blocks
- file locations
- tiny symbol lists or anchor snippets
- one key takeaway

At this layer, answer the central mechanism directly. Do not stay only at the framing level.

### Layer 2: Example-driven walkthrough

Use when the user wants to understand how data moves, how memory is laid out, how numbering or mapping works, or how two layers cooperate.

Use one fixed example with concrete numbers and keep reusing it.

Preferred order:

1. where data lives
2. how it is indexed
3. how it is written
4. how it is read
5. how sharing/reuse works

Code at this layer should be anchor snippets, not full functions.

### Layer 3: Code-level drilldown

Use when the user asks about one function, one branch, one expression, one line, or asks to continue deeper.

At this layer:

- paste the minimal relevant code
- explain mainly through inline comments
- keep the prose between code blocks very short

## Search Workflow

Start from the narrowest reliable anchor:

- exact function or method name
- class plus method
- enum / opcode / event constant
- log string
- config key
- known file from the user's tabs

Prefer repository search over broad reading. Use `rg` first. Read only the files needed for the current layer.

When tracing indirect control flow, check:

- wrapper -> helper -> implementation
- interface / virtual -> concrete override
- callback registration -> callback body
- queue push -> worker loop -> handler
- scheduler select -> executor submit -> worker run
- Python binding -> C/C++ bridge -> native implementation
- config / feature flag / mode switch -> branch-specific path

Read [references/search-patterns.md](references/search-patterns.md) when the path crosses language boundaries, threads, processes, or registration-based dispatch.

## Concept-First Expansion

Expand by concept blocks, not by search order.

Preferred concept blocks:

- which layer owns what
- key state or data structure
- one critical function
- how adjacent layers connect
- one real example

Bad pattern:

- "I searched A, then B, then C"

Good pattern:

- "Python decides reuse"
- "runner translates tables"
- "C++ consumes slot/block metadata"
- "the essence is X, the rest is support machinery"

## Code Presentation Rules

- Put explanations into code comments whenever possible.
- If a sentence only explains the current example, keep it in the example comment instead of separate prose.
- Prefer the smallest snippet that proves the point.
- Each code block should answer one question only.
- Before a code block, allow at most one short guiding sentence.

Prefer:

```text
seq0.block_table = [7, 8, 44, 45]
seq1.block_table = [7, 8, 88, 89]   # 前两个 block 共享，后两个分叉
```

Avoid:

- showing the same point in code and then restating it below in prose
- long function dumps in the first reply
- multiple code blocks that all prove the same thing

## Compression Rules

Default shape per reply:

- 1 subject sentence
- 1 short tree/flow sketch if multi-layer
- 2-4 content blocks
- 1 key takeaway or next-step suggestion

Prefer answering one dense central question over giving a thin broad survey.

Do not:

- repeat the same conclusion in multiple sections
- add a summary after every section
- write long transitional prose
- dump full evidence lists unless the user asked for strict tracing

## Source Location Rules

Always give enough location for the user to continue reading.

- Default: file path for each concept block
- Important entry points: precise file link with line number
- Only surface 3-5 key locations in one reply unless the user asks for more

The pasted code should match the cited location.

## Architecture Sketch Rules

When the answer involves two or more layers or modules:

- first screen must include a 3-7 line tree-style architecture sketch
- the sketch is the map; every later section should map back to one node in it
- the sketch should show the main handoff only, not every branch

Use this style:

```text
LayerA
└─ handoff_1
   └─ LayerB
      └─ handoff_2
         └─ LayerC
```

## Example-Driven Rules

When memory layout, indexing, mapping, or data flow is the point, prefer a fixed numeric example.

Use one consistent set of numbers across the whole reply.

Good example topics:

- arena shape
- `block_id -> slot`
- `seq.block_table`
- cache hit vs miss
- shared prefix reuse

At this depth, numbers usually teach better than prose.

## Full Chain Mode

Only switch to full-chain mode when the user asks for:

- `完整链路`
- `怎么走到这`
- `谁调到谁`
- strict source-backed tracing

In full-chain mode:

- still start with the subject sentence and architecture sketch
- then show the main chain
- keep evidence concise and tied to file locations

Do not use full-chain mode by default for broad implementation questions.

## Runtime Boundary Rules

When you cross a boundary, call it out briefly:

- thread
- process
- coroutine/event loop
- Python/C API
- RPC/IPC
- kernel/runtime boundary

Say only:

- where work is decided
- where work is executed
- what metadata crosses the boundary

## Teaching Close

End with one of these, not a full recap:

- one key takeaway
- one sharp distinction
- one suggested next step

Examples:

- `真正关键点：前缀命中只在 Python 决定，C++ 只消费 block_id/slot。`
- `下一步最值得只看 allocate() 命中逻辑。`
- `如果继续下钻，先看 slot = block_id * block_size + offset。`

## Density Rule

Prioritize the essence over staged withholding.

- If the user asks a broad implementation question, answer the core mechanism immediately.
- Do not force a low-density "skeleton only" first reply.
- Compress by removing secondary detail, not by removing the main idea.
- A good first reply should let the user walk away with the right mental model even if they stop there.
