# paper-to-code

Reading recent AI papers, mostly on **LLM agents**, some on **AI infrastructure**, and rebuilding each core idea as a small, readable implementation.

The rule for every paper: **explain it simply, and write down what it teaches me about building agents.** If an idea gets used in a real system, I link it. If I can't explain it, I don't understand it yet.

## Papers

| # | Paper | Year | Main lesson | Status |
|---|---|---|---|---|
| 01 | [ReAct](01-react/) | 2022 | Think before and after every tool call | ✅ |
| 02 | SWE-agent: Agent-Computer Interfaces | 2024 | — | ⬜ |
| 03 | Why Do Multi-Agent LLM Systems Fail? | 2025 | — | ⬜ |
| 04 | Mem0: long-term memory for agents | 2025 | — | ⬜ |
| 05 | Agentic Context Engineering (ACE) | 2025 | — | ⬜ |

⬜ not started · 🟨 in progress · ✅ done

## Folder layout

Each paper gets one folder (`NN-short-name/`) with:
- `README.md`: my 1-page explanation (see [`_template/README.md`](_template/README.md))
- (optional) where I applied the idea in a real project
- `notes.md` (optional): rough notes, open questions

## Running

```bash
cp .env.example .env   # add your own API key
pip install -r requirements.txt
python 01-react/main.py
```
