# true-needs

Guide users to uncover true intent beyond surface requests via first-principles thinking.

## What It Does

true-needs is a Claude skill that helps users clarify what they *actually* need before taking action. Users often ask for a means ("找个画图工具") when they have a different end goal. This skill translates nouns into verbs and adjectives into constraints through a structured but natural conversation.

Key features:
- **First-principles questioning** — break down surface requests to underlying needs
- **One question per round** — conversational, not interrogative
- **Skip when clear** — if the user's request is already specific, proceed directly
- **Better alternatives** — propose solutions the user hasn't considered
- **Structured output** — produces a Search Result block that downstream skills can consume

## How It Works

| Step | What |
|------|------|
| 1. Diagnose | Is the request clear enough? Extract parameters or identify gaps |
| 2. Core scenario | "这个工具你打算用在什么场景？" — highest-level question first |
| 3. Constraints | Tech stack, environment, format preferences |
| 4. Exclusions | "有没有确定不需要的？" — elimination approach |
| 5. Reconstruct | Propose better alternatives if found |

## Example

**User**: "找个好用的画图工具"

> 这个画图工具你主要用在什么场景？
> 1. 生成流程图/架构图（比如 mermaid、draw.io 类）
> 2. 像素艺术/精灵图生成（比如 sprite-gen 类）
> 3. 数据可视化图表（比如 matplotlib 类）

**User**: 够了，开始搜索

**Output**:
```
# Search Result
- query: "sprite"
- hints: ["pixel art", "procedural", "svg", "animation", "css"]
- excludes: []
- domain: "frontend_design"
- top_n: 5
```

## Integration

Designed as a pre-search refinement step for [gh-finder](https://github.com/StarAI26/gh-finder), but the questioning framework applies to any ambiguous request.

## License

MIT
