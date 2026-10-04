# agent-paper-notes

My notes on recent papers about **LLM agents**: what problem each paper solves, how it solves it, where it falls short, and what it teaches me about building agents.

The rule for every paper: **explain it simply, and write down what it changes about how I build agents.** If I can't explain it simply, I don't understand it yet.

## Papers

| # | Paper | Year | Main lesson | Status |
|---|---|---|---|---|
| 01 | [ReAct](01-react/) | 2022 | Think before and after every tool call | ✅ |
| 02 | [Building Effective Agents](02-building-effective-agents/) (Anthropic, blog post) | 2024 | Use the simplest design that works; tools are an interface for the model | ✅ |
| 03 | Why Do Multi-Agent LLM Systems Fail? | 2025 | — | ⬜ |
| 04 | Mem0: long-term memory for agents | 2025 | — | ⬜ |
| 05 | SWE-agent: Agent-Computer Interfaces | 2024 | — | ⬜ |

⬜ not started · 🟨 in progress · ✅ done

## Format

Each paper gets one folder (`NN-short-name/`) with a `README.md` (see [`_template/README.md`](_template/README.md)):
1. **The problem**: what wasn't working before
2. **How it solves it**: the key idea, explained simply
3. **Limitations**: what the paper admits, and what I noticed
4. **What I learned for building agents**
5. *(optional)* where I applied the idea in a real project

Paper PDFs stay local; each README links to the original.
