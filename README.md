# 🏭 Industrial AI Quality Assistant

> A manufacturing quality-analysis assistant built on **Agentic AI + RAG + Knowledge Graph**.
> Describe a defect in plain language; the agent traces the root cause, looks up the relevant standard, and writes a structured report.

[中文说明](#中文说明)

![Python](https://img.shields.io/badge/Python-3.10+-3776AB?logo=python&logoColor=white)
![LangChain](https://img.shields.io/badge/LangChain-tool%20calling-1C3C3C)
![Streamlit](https://img.shields.io/badge/UI-Streamlit-FF4B4B?logo=streamlit&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-green)

---

## Highlights

- **Agent-driven, not if-else** — the LLM decides which tools to call and in what order.
- **Evidence-based answers** — RAG retrieves from quality-standard documents instead of letting the LLM guess.
- **Root-cause tracing** — a knowledge graph links *Defect → Station → Supplier / Operator → Change record*.
- **Auditable** — every tool call and its output is shown in the UI.

## Architecture

```
User question (defect description / station / standard query)
        │
        ▼
┌──────────────────────────────────────────────────────┐
│            Agent (LLM tool-calling loop, ≤5 rounds)   │
├──────────────────┬──────────────────┬────────────────┤
│ search_quality_  │ trace_defect_    │ generate_      │
│ standard         │ source           │ report         │
│ RAG over quality │ Knowledge-graph  │ Structured     │
│ standard docs    │ root-cause trace │ quality report │
└──────────────────┴──────────────────┴────────────────┘
        │
        ▼
Root cause + cited standard + recommended actions
```

## Tech Stack

| Component        | Technology                                      |
| ---------------- | ----------------------------------------------- |
| LLM              | GLM-4 (Zhipu AI) via OpenAI-compatible API      |
| Agent            | LangChain tool calling + custom loop            |
| Vector store     | FAISS                                           |
| Embeddings       | `sentence-transformers/all-MiniLM-L6-v2`        |
| Knowledge graph  | NetworkX                                        |
| UI               | Streamlit                                       |

## Project Structure

```
INDUSTRIAL-AI-ASSISTANT/
├── app.py                # Streamlit app (entry point)
├── agent_main.py         # Command-line agent demo
├── rag.py                # Standalone RAG demo
├── knowledge_graph.py    # Knowledge graph + defect tracing
├── config.example.py     # API config template
├── requirements.txt
└── data/quality_docs/    # Sample quality-standard documents (simulated)
    ├── 螺纹孔检测标准.txt
    ├── AOI缺陷分类指南.txt
    └── 产线异常处理流程.txt
```

## Quick Start

```bash
git clone https://github.com/clarkechan/INDUSTRIAL-AI-ASSISTANT.git
cd INDUSTRIAL-AI-ASSISTANT
python -m venv .venv
.venv\Scripts\activate          # Windows  (macOS/Linux: source .venv/bin/activate)
pip install -r requirements.txt

cp config.example.py config.py  # then fill in your API key
```

Run:

```bash
streamlit run app.py        # full web app
python agent_main.py        # agent demo in the terminal
python rag.py               # RAG module only
python knowledge_graph.py   # knowledge-graph tracing only
```

> The embedding model is downloaded from Hugging Face on first run.

## Example

**Input:** `CNC-03工站最近螺纹孔异物缺陷增多，请帮我分析原因并给出处理建议`

**Agent trace:**

```
[Tool] trace_defect_source("螺纹孔异物")
       → Station CNC-03 (Line-2), Supplier A
       → ⚠️ Recent change: 2026-02 tooling batch change
       → ⚠️ Station change: 2026-01 equipment maintenance

[Tool] search_quality_standard("螺纹孔异物 判定标准")
       → Grade A: >0.5mm² → NG; Grade B: 0.1–0.5mm² → manual re-check

[Tool] generate_report(...)
       → Defect summary / Root cause / Impact / Actions / Follow-ups
```

**Conclusion:** the most likely cause is Supplier A's February tooling batch change. Isolate the affected batch, notify the supplier, and open an 8D report.

## Design Principles

| Principle        | How                                                                  |
| ---------------- | -------------------------------------------------------------------- |
| **Controllable** | The agent can only use the three defined tools; max 5 rounds.        |
| **Observable**   | Every tool call, input and output is logged and shown in the UI.     |
| **Explainable**  | Conclusions must come from tool results; the reasoning chain is visible. |

In industrial settings, reliability and auditability are non-negotiable — these three principles are what make an LLM agent deployable on a production line.

## Background

This project grew out of hands-on work on AOI (Automated Optical Inspection) systems for Apple supply-chain projects, including:

- Dual-robot concurrent inspection with state-machine scheduling
- Integrating Python AI inference services into C#/.NET industrial software via REST APIs
- Traceability platforms connecting defect data across production sites

It explores the next step: moving from hard-coded workflows to adaptive, LLM-driven agents.

## Roadmap

- [ ] Neo4j for a production-grade knowledge graph
- [ ] Multi-agent collaboration (quality / logistics / maintenance)
- [ ] Live integration with MES / WMS
- [ ] Model evaluation and drift monitoring

---

<a name="中文说明"></a>

## 中文说明

### 项目简介

一个面向制造业的**工业质量智能分析助手**。用自然语言描述缺陷问题，Agent 会自主调用工具：用**知识图谱**追溯根因，用 **RAG** 检索质量标准文档，最后生成结构化的质量分析报告。

### 核心亮点

- **Agent 自主决策**：由大模型决定调用哪些工具、按什么顺序，而不是写死的 if-else
- **回答有据可查**：结论来自质量标准文档，而不是大模型凭空生成
- **链式根因追溯**：缺陷 → 工站 → 供应商 / 操作员 → 变更记录
- **可控、可观测、可解释**：每一步工具调用都会在界面中展示，便于审计

### 适用场景

- 产线质量异常分析
- 缺陷根因追溯
- 质量标准查询
- 自动生成质量分析报告

### 快速开始

```bash
pip install -r requirements.txt
cp config.example.py config.py   # 填入你的 API Key
streamlit run app.py
```

> 文档库中的质量标准为模拟数据，仅用于演示。

---

## License

MIT

## Author

**Clarke Chan（陈星宇）** · [Blog](https://mrcxy.com) · [GitHub](https://github.com/clarkechan)

2.5 years of industrial software development for the Apple supply chain.
