# ReAct: Synergizing Reasoning and Acting in Language Models

**Paper:** Yao et al., 2022 (ICLR 2023). https://arxiv.org/abs/2210.03629
**Read on:** 2026-10-03

## 1. The problem

Before ReAct, LLMs were used in one of two ways:

- **Reason only (chain-of-thought):** the model thinks step by step, but only from its own memory. If it remembers a fact wrong, nothing corrects it, and every later step builds on the hallucinated fact.
- **Act only:** the model calls tools (e.g. a Wikipedia search) and gets real observations back, but it doesn't think about what they mean. It doesn't know how to use what it found, so it loses direction on longer tasks.

## 2. How it solves it

ReAct **interleaves** reasoning and acting in a loop:

```
Thought → Action → Observation → Thought → Action → Observation → ... → Answer
```

- **Thinking guides acting.** A thought decides *what* to look up next and *why*. It also breaks the goal into steps, tracks progress, and changes the plan when something goes wrong (e.g. a search returns nothing).
- **Acting grounds thinking.** Real observations come back from tools, so the next thought is based on what was *found*, not what the model *remembers*. This reduces hallucination compared with chain-of-thought alone.

How it actually runs: each step is a separate LLM call. The model only *writes* an action like `search[X]` and stops. The program executes it, appends the result as an observation, and calls the model again with the longer history. The model learns this format from a few examples in the prompt (few-shot prompting, no training).

The paper tests it on question answering (HotpotQA), fact checking (FEVER), a text-based household game (ALFWorld) and a simulated shopping website (WebShop).

One comparison stood out to me: an Inner Monologue–style baseline, whose thoughts only *report* the current state ("I see a closed cabinet"), did worse on ALFWorld than ReAct, whose thoughts *reason* about the goal and what to do next. **Having thoughts isn't enough. They need to plan and explain.**

## 3. Limitations

| Limitation | Possible solutions (to explore in later papers) |
|---|---|
| **Cost and latency.** Every step is another LLM call, and the prompt grows each time. | Use a cheaper model for easy steps; prompt caching (the repeated prefix gets cheaper and faster); use a fixed workflow when the task doesn't need a full agent. |
| **Long context → losing the goal.** As observations pile up, the original goal gets buried. | Restate the goal regularly (e.g. a to-do list rewritten near the end of the context); summarize old steps; hand sub-tasks to sub-agents with a clean context; keep notes in external memory. |
| **Depends on tool quality.** An unhelpful or misleading search result derails the next thoughts. | Fallback tools and retries; better tool design; error messages that tell the model what to try next (e.g. "No results. Try a shorter query."). |
| **Repetitive loops.** The agent can repeat the same action without making progress. | Detect repeats **in code**, not by trusting the model (same action + same input N times → stop or redirect); a hard cap on the number of steps. |
| **Still far from human experts on WebShop.** Humans explore more products and rewrite their search queries more often. | Training agents (fine-tuning / reinforcement learning) instead of only prompting them; see Search-R1 on my reading list. |

These solutions are my current understanding, not proven answers. Later papers (context engineering, memory, multi-agent failures) should show which ones actually work.

## 4. What I learned for building agents

- **Think before calling a tool.** The agent should say *why* it needs the tool and what it expects to get, not call tools blindly.
- **Think after the tool returns.** Analyze the observation: did it answer the question? What's still missing? What should happen next? That analysis becomes the next thought.
- **Thoughts should plan, not just describe.** "What do I still need, and why?" beats "here is what I see."
- **Put safety checks in code, not in the prompt.** Step limits and loop detection should not depend on the model noticing its own mistakes.
- **Design tools for the model.** Clear outputs and helpful error messages matter as much as the model itself.

---

## Applied to my project
_TODO: the negotiation trainer v2 planner will be a ReAct-style agent (thinks before and after each tool call, with a step cap and loop detection in code). Link the code here once it's built._
