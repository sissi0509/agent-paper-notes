# Building Effective Agents

**Source:** Erik Schluntz & Barry Zhang, Anthropic, December 2024 (engineering blog post, not a research paper). https://www.anthropic.com/engineering/building-effective-agents
**Read on:** 2026-10-03

## 1. The problem

Many teams building with LLMs reach for complex agent frameworks and autonomous agents by default, even when a simpler design would work better. Complexity adds cost, latency and compounding errors, and frameworks can hide what's actually sent to the model, which makes debugging hard. The post is a practical guide, from Anthropic's experience with customers, on **what to build, and when**.

## 2. How it solves it

**Two kinds of agentic systems:**
- **Workflows:** LLMs and tools follow **predefined steps written in code**. Best when the task is predictable.
- **Agents:** the LLM **decides its own steps**: which tools to call, what to do next, and when to stop. Best for open-ended tasks where the steps can't be predicted.

**Start simple.** Often a single LLM call (with retrieval and good examples) is enough. Add complexity only when it measurably helps.

**On frameworks:** they speed things up, but they can hide prompts and behavior. Start with the API directly; if you use a framework, understand what it does underneath.

**The building block** is the *augmented LLM*: an LLM plus retrieval, tools and memory.

**Five workflow patterns + agents:**

| Pattern | What it is | When to use it |
|---|---|---|
| **Prompt chaining** | Fixed sequence of steps; each takes the previous output as input. Optional **gates** (code checks) between steps. | The task splits cleanly into fixed subtasks. Trades latency for accuracy. |
| **Routing** | Classify the input (with an LLM or code), then send it to **one** specialized path. | Distinct categories that are **known in advance**, e.g. refund vs. tech support; easy → cheap model, hard → strong model. |
| **Parallelization: sectioning** | **Different** subtasks, fixed in code, run at the same time, then merged. | Independent parts, e.g. one call answers while another checks for inappropriate content. |
| **Parallelization: voting** | The **same** task run several times, then combined by vote/average. | When you need more confidence, e.g. several checks for security bugs. |
| **Orchestrator-workers** | A central LLM **invents** the subtasks at runtime, delegates them to workers, then combines the results. | You can't know the subtasks in advance, e.g. which files a code change touches. |
| **Evaluator-optimizer** | One LLM generates, another evaluates **and gives feedback**; loop until accepted. | Clear criteria exist **and** feedback actually improves the result, e.g. literary translation. |
| **Agent** | The LLM decides each action, gets **ground truth from the environment** (tool results), and loops until the goal is met or a limit is hit. Can pause for human checkpoints. | Open-ended problems, e.g. coding agents, computer use. Needs sandboxed testing and guardrails. |

How I tell the confusing ones apart:
- **Routing *chooses* a path; the orchestrator *creates* the tasks.**
- **Sectioning vs. orchestrator:** same shape (split → run → combine), but sectioning's split is fixed in code, while the orchestrator's split is decided by an LLM.
- **Sectioning vs. voting:** different questions vs. the same question. If you drop one call, sectioning loses a piece of the answer, while voting only loses some confidence.
- **Evaluator-optimizer vs. agent:** the evaluator loop is fixed (generate → evaluate → repeat) and refines its own output; an agent chooses actions and learns from the environment.

**Three core principles:**
1. **Simplicity:** keep the design as simple as possible.
2. **Transparency:** make the agent's planning steps visible (in logs for developers, in the UI for users).
3. **A well-designed agent-computer interface (ACI):** document and test tools as carefully as a human-facing interface.

**Tool design tips (appendix):**
- Don't make the model commit to details before it has worked them out (e.g. a diff header that states the line count *before* the code is written).
- Use formats the model has seen a lot on the internet (code in a markdown block, not escaped inside JSON).
- Avoid **formatting overhead**, i.e. bookkeeping that isn't the real task, like counting lines or escaping quotes.
- **Poka-yoke (mistake-proofing):** their coding agent made errors with relative file paths, so they required absolute paths, and the errors stopped.

## 3. Limitations

- **It's a guide from experience, not an experiment.** There are no measured comparisons showing how much each pattern helps; the advice is practical judgment.
- **The boundaries between patterns are blurry.** An orchestrator that loops starts to become an agent. Real systems mix patterns, so the categories are a vocabulary, not strict rules.
- **"Use the simplest design that works" needs evaluation to apply.** You can't know whether a pattern "measurably helps" without evals, and the post doesn't go deep into how to build them.
- **Written in December 2024.** Some details (e.g. specific tools) may change as the field moves.

## 4. What I learned for building agents

- **Complex ≠ better.** A fancy architecture *looks* skillful, but good engineering is choosing the simplest design that works, and adding complexity only when it measurably helps.
- **Pick a pattern only when it fits.** E.g. routing needs categories known in advance. If I can't list them, a router would just guess.
- **ACI is HCI for models.** The "user" of my tools is the model, so I should design tool names, descriptions and formats from its point of view, the same way I'd think about a human user. My HCI background is an advantage here.
- **Let the LLM do the real task in an easy format, and let code do the bookkeeping.** If code can compute it (counts, IDs, paths), the model shouldn't have to.
- **Ask for reasons, but make them checkable.** A stated reason can itself be hallucinated. Ask the model to cite specific evidence (e.g. a transcript turn number), then verify it in code.
- **Frameworks:** use them for the plumbing (state saving, human-in-the-loop, streaming), write the logic myself, use the low-level APIs, and always be able to see the exact prompts sent to the model.
