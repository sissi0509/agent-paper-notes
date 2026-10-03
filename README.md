# paper-to-code

Reading recent AI papers, mostly on **LLM agents**, some on **AI infrastructure**, and rebuilding each core idea as a small, readable implementation.

The rule for every paper: **explain it simply, then apply one idea to a real system**, my multi-agent negotiation-training platform. If I can't explain it, I don't understand it yet.

## Papers

| # | Paper | Year | Applied to my project | Status |
|---|---|---|---|---|
| 01 | [ReAct](01-react/) | 2022 | Planner as a ReAct agent (planned) | 🟨 |
| 02 | SWE-agent: Agent-Computer Interfaces | 2024 | — | ⬜ |
| 03 | Why Do Multi-Agent LLM Systems Fail? | 2025 | — | ⬜ |
| 04 | Mem0: long-term memory for agents | 2025 | — | ⬜ |
| 05 | Agentic Context Engineering (ACE) | 2025 | — | ⬜ |

⬜ not started · 🟨 in progress · ✅ done

## Folder layout

Each paper gets one folder (`NN-short-name/`) with:
- `README.md`: my 1-page explanation (see [`_template/README.md`](_template/README.md))
- an "Applied to my project" section linking to where the idea is used
- `notes.md` (optional): rough notes, open questions

## Running

```bash
cp .env.example .env   # add your own API key
pip install -r requirements.txt
python 01-react/main.py
```
