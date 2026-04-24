---
name: true-needs
category: meta
version: 1.0
description: Guide users to uncover true intent beyond surface requests via first-principles. / 引导用户跳出表象需求，通过第一性原理还原真实意图。
---

# Needs Scout

> "We reason from first principles, not by analogy."

First-principles thinking is a tool to find better solutions — never a weapon to interrogate the user.
使用第一性原理是思考工具，用来找到更优解，绝不是质问用户的武器。

## When to Trigger

This skill is **primarily designed as the pre-search refinement step for `gh-finder`**, but its questioning framework applies to any ambiguous request.

- **Trigger when**: User request is vague, mentions a means rather than a goal, or lacks key context (e.g., "找个好用的画图工具", "推荐个 CLI").
- **Skip when**: User already provided clear query + constraints + scope. Extract parameters directly — do NOT ask questions.
- **gh-finder integration**: In `gh-finder`, offer this when intent is ambiguous. **Do NOT force a yes/no prompt on every search request.** If the user's request is already specific, proceed directly to search.

## Questioning Flow

Users ask for a *means*, not the end goal. Translate nouns into verbs, adjectives into constraints.
用户要的是"手段"。把名词翻译成动词，形容词翻译成约束。

### Step 1: Diagnose — is it clear?

Scan the user's request for these signals:

| Signal | Action |
|--------|--------|
| Clear query + tech/domain + constraints | Extract parameters, skip to Output |
| Only a vague noun ("找个XX") | Ask Step 2 |
| Mixed clarity (goal clear, scope missing) | Ask about the missing dimension only |

### Step 2: Core scenario (always first, if needed)

Start with the highest-level question. One question, conversational tone.

**Template**: "这个工具你打算用在什么场景？比如：[2-3 likely scenarios based on the request]"

- Give 2-3 options ordered by likelihood (your best guess first)
- Core dimensions = positive selection ("选你想要的")
- Let the user reject or add their own

### Step 3: Constraints + Exclusions (as needed)

After the core scenario is clear, ask about specifics. Use elimination for fine-grained items.

**Constraints template**: "有偏好的技术栈或运行环境吗？比如：[2-3 options]"

**Exclusions template**: "有没有确定不需要的？比如：[2-3 common exclusions]"

- Fine-grained items = elimination ("排除你不要的，剩下的默认需要")
- If user skips, that dimension doesn't matter — move on

### Step 4: Reconstruct — better alternatives

During questioning, think: is there a solution the user hasn't considered? If yes, propose it naturally in the next question.

**Template**: "你提到的[用户说的]，其实[alternative]可能更合适，因为[1-sentence reason]。要看看这个方向吗？"

Do NOT lecture. One sentence max.

### Rules for All Rounds

- **One question per round** — never stack questions.
- **Skip expressed preferences** — if user already said "要 Python 的", don't ask about language.
- **Options are your hypothesis** — order by likelihood. User selecting "Other" means your framing missed; re-think the next question.
- **When enough info**: offer "够了，开始搜索" as an option. Let the user confirm — don't stop unilaterally.

## Dialogue Examples

### Example 1: Vague request → refinement

**User**: "找个好用的画图工具"

**Round 1**:
> 这个画图工具你主要用在什么场景？
> 1. 生成流程图/架构图（比如 mermaid、draw.io 类）
> 2. 像素艺术/精灵图生成（比如 sprite-gen 类）
> 3. 数据可视化图表（比如 matplotlib 类）

**User**: 2

**Round 2**: (user picked a direction, now ask constraints)
> 有偏好的输出格式吗？
> 1. SVG + CSS 动画（适合前端集成）
> 2. PNG 静态图
> 3. 都行，质量好最重要

**User**: 够了，开始搜索

→ Extract → query: "sprite", hints: ["pixel art", "procedural", "svg", "animation", "css"], excludes: [], domain: "frontend_design", top_n: 5

### Example 2: Request already clear → skip

**User**: "找一个 Python 的 ASCII 文字转图案工具，支持自定义字体，不要 web 版"

→ Already clear. Extract directly:
query: "ascii art", hints: ["figlet", "banner", "font", "text", "terminal"], excludes: ["web", "browser", "javascript"], domain: "general", top_n: 5

No questions needed.

## When to Stop

- User selects "够了，开始搜索" → generate Search Result, proceed.
- User skips a round → that dimension doesn't matter, don't push.
- User gives a complete answer in one round → skip remaining rounds.

## Output: Search Result

After the conversation (or direct extraction), output this structured block for `gh-finder` to consume directly:

```
# Search Result

- **query**: 1-2 core keywords (do NOT stack — GitHub Search API uses AND logic, longer queries return zero results)
- **hints**: 3-6 related synonyms / tech terms — agent proposes based on its understanding, user just confirms the direction, not required to understand each term.
  agent 根据对话理解提出 tech terms，用户只需确认方向是否对，不需要理解每个术语。
- **excludes**: explicitly unwanted items
- **domain**: general | frontend_design | search_tool | skill | devops
- **top_n**: 5 (default)
- **reasoning**: 1-2 sentences explaining the mapping from user needs to search parameters — why these keywords were chosen.
  从用户需求到搜索参数的映射逻辑，1-2 句话说明为什么选这些词。
```
