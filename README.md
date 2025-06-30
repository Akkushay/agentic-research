# Agentic AI Research & Personalization Pipeline

## 1. Overview
This repository delivers a **modular**, **test‑driven**, **multi‑agent** platform that:
1. **Surveys cutting‑edge agentic AI research**  
2. **Summarizes and evaluates** new methods against failure‑mode taxonomies  
3. **Extends** to personalized services (e.g., property research, email management)

Key technologies:
- **LangGraph**: Defines your pipeline as a graph of agents and tools.  
- **Langfuse**: Instruments every step for monitoring, auditing, and diagnosing MAST F1–F14 failures.  
- **TDD‑First**: All new features begin with a failing unit test.  
- **DAWN‑Style Registry & Policies**: Dynamic discovery of tools with built‑in compliance and governance checks.

---

## 2. Curated Research Papers & Summaries

Place each PDF under `research/pdfs/`, and maintain structured entries in `research/papers.md`:

| Paper & Link                                                                                                  | Objective                                                                                                    | Novelty                                                                                                            | Significance                                                                                                                |
|--------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------|
| **ReAct**<br>Yao et al. (2023)<br>https://arxiv.org/abs/2210.03629                                           | Interleave chain‑of‑thought (CoT) reasoning with external tool invocations.                                   | First to unify “thinking” and “acting” in LLM workflows.                                                           | Higher interpretability and accuracy on complex QA and decision tasks.                                                      |
| **Reflexion**<br>Shinn et al. (2023)<br>https://arxiv.org/abs/2303.11366                                      | Agents self‑reflect on failures in natural language and store reflections as memory.                         | Verbal‑RL style with no parameter updates—reflections fed back as memory.                                          | Enables iterative self‑improvement across sessions without retraining.                                                      |
| **DAWN**<br>Aminiranjbar et al. (2024)<br>https://arxiv.org/abs/2410.22339                                    | Global registry/discovery framework for LLM‑based agents with safety, security, and compliance layers.      | Distributed agent registry with policy layers, trust anchors, and dynamic scaling.                                 | Enterprise‑grade governance and automated compliance for large multi‑agent ecosystems.                                      |
| **MAST Failure Taxonomy**<br>Cemri et al. (2025)<br>https://arxiv.org/abs/2503.13657                        | Empirically catalog 14 common failure modes (F1–F14) in multi‑agent LLM systems.                             | First systematic, data‑driven taxonomy of multi‑agent failures.                                                     | Guides targeted diagnostics and mitigation via trace analysis.                                                               |
| **CoT Prompting**<br>Wei et al. (2022)<br>https://arxiv.org/abs/2201.11903                                    | Elicit step‑by‑step reasoning from LLMs via explicit CoT prompt templates.                                  | Demonstrates up to 18% accuracy gains by surfacing latent reasoning.                                                | Foundation for transparent, interpretable reasoning in agentic pipelines.                                                    |
| **Fast vs. Slow Thinking**<br>DeepMind (2024)<br>https://arxiv.org/pdf/2410.08328                             | Map dual‑process human cognition (System 1 vs. System 2) to AI agent architectures.                         | Formalizes hybrid pipeline balancing rapid heuristics with deliberative reasoning.                                  | Enables dynamic switching between speed and depth—key for both research summarization and real‑time personalization tasks. |


## 3. Strategy & Pipeline Design

We orchestrate our platform as a **LangGraph** of specialized agents:

1. **PrincipalAgent (Judge)**  
   - Receives a user’s high‑level objective (e.g., “Survey new agentic AI research”).  
   - Coordinates sub‑agents and makes final “go/no‑go” decisions.

2. **RegistryAgent**  
   - Dynamically discovers all tools and policies (DAWN‑style).  
   - Provides lookup for ServiceAgent and enforces compliance rules.

3. **TaskAgent**  
   - Decomposes objectives into ordered subtasks:  
     - `fetch_papers`  
     - `summarize_papers`  
     - `reflect_on_results`  
     - `evaluate_papers`

4. **ServiceAgent**  
   - Facade for invoking registered tools (e.g., `arxiv_fetcher`, `text_summarizer`, `react_adapter`, `email_service`, `property_api`).  
   - Handles authentication, rate limits, and retries.

5. **ExecutorAgent**  
   - Executes each subtask via ServiceAgent, capturing both:  
     - **System 1** (fast heuristics)  
     - **System 2** (detailed CoT reasoning via prompts)

6. **CriticAgent**  
   - Scores outputs on:  
     - Relevance & Novelty metrics  
     - MAST F1–F14 failure‑mode checks (via Langfuse trace analysis)  
   - Flags potential failure‑mode violations.

7. **EvaluationAgent**  
   - Aggregates scored results and flagged issues into a final Markdown report:  
     - Title, link, summary, score, failure‑mode tags  
   - Feeds back to PrincipalAgent for re‑runs or refinement.

8. **Instrumentation**  
   - **Langfuse** traces inputs, outputs, timings, and errors for every node.  
   - Dashboards visualize trends, failure‑mode frequencies, and system health.

## 4. Project Structure

```text
agents/
  ├─ principal.py       # Orchestrates Judge loops & LangGraph assembly
  ├─ registry.py        # RegistryAgent for tool & policy lookup
  ├─ task.py            # TaskAgent decomposition logic
  ├─ service.py         # ServiceAgent facade for tool invocation
  ├─ executor.py        # ExecutorAgent with System 1/System 2 support
  ├─ critic.py          # CriticAgent with MAST F1–F14 evaluation
  └─ evaluator.py       # EvaluationAgent report generator (Judge)

tools/
  ├─ arxiv_fetcher.py   # @tool: fetch_recent_agentic_papers
  ├─ text_summarizer.py # @tool: summarize_text
  ├─ react_adapter.py   # @tool: apply_react_pattern
  ├─ reflexion_module.py# @tool: apply_reflexion
  ├─ dawn_registry.py   # @tool: dawn_agent_registry
  ├─ email_service.py   # @tool: read/send Gmail via OAuth2
  └─ property_api.py    # @tool: fetch_property_data

instrumentation/
  ├─ langfuse_setup.py  # Initialize Langfuse client & configuration
  └─ langgraph_nodes.py # Node wrappers emitting spans

research/
  ├─ pdfs/              # Downloaded paper PDFs
  └─ papers.md           # Structured summaries

tests/
  ├─ test_arxiv_fetcher.py
  ├─ test_text_summarizer.py
  ├─ test_registry.py
  ├─ test_task.py
  ├─ test_service.py
  ├─ test_executor.py
  ├─ test_critic.py
  └─ test_evaluator.py

agentic_pipeline.py    # Entry point: builds & runs the LangGraph
requirements.txt       # Pin versions for reproducibility
README.md              # This file
```

## 5. Security, Risk & Governance

- **OAuth2** for all third‑party integrations, with token encryption at rest.  
- **DAWN‑Style Policy Registry** enforces which tools/agents can run under which contexts.  
- **Langfuse Audit Logs** create tamper‑evident trails of all actions.  
- **Data Lineage** tracked end‑to‑end to support GDPR/CCPA compliance.  
- **Sandboxing & Threat Modeling** around tool execution to prevent code injection or unauthorized access.

## 6. Development Workflow (TDD)

1. **Write a failing test** in `tests/` for your next feature.  
2. **Implement minimal code** in `agents/` or `tools/` to satisfy the test.  
3. **Refactor** safely—tests guard against regressions.  
4. **Commit** to `feature/<tool-or-agent>`.  
5. **Open PR**: CI runs `pytest`, linting, security scans, and policy checks.  
6. **Merge** once all checks pass.

## 7. To‑Do & LLM Guidance

These tasks keep the LLM (and team) on track by embedding next steps directly here:
## 6. Next Steps

Follow these steps **from a fresh repo**—each feature must ship complete, production‑ready code (no placeholders), backed by passing tests:

1. **Environment Setup**  
   - Create & activate a Python venv:  
     ```bash
     python3 -m venv .venv
     source .venv/bin/activate
     ```  
   - Create `requirements.txt` with:
     ```bash
     arxiv
     langchain
     pytest
     langfuse
     langgraph
     ```
   - Install:
     ```bash
     pip install -r requirements.txt
     ```
2. **Project Scaffold**  
   ```bash
   mkdir -p agents tools instrumentation research/pdfs tests
   touch agents/__init__.py tools/__init__.py instrumentation/__init__.py research/papers.md
   ```
	- **agents/**:  
	  - `principal.py`  
	  - `registry.py`  
	  - `task.py`  
	  - `service.py`  
	  - `executor.py`  
	  - `critic.py`  
	  - `evaluator.py`  

	- **tools/**:  
	  - `arxiv_fetcher.py`  
	  - `text_summarizer.py`  
	  - `email_service.py`  
	  - `property_api.py`  
	  - (and any future `@tool` modules)  

	- **instrumentation/**:  
	  - `langfuse_setup.py`  
	  - `langgraph_nodes.py`  

	- **research/pdfs/**:  
	  - place downloaded PDFs here  

	- **research/papers.md**:  
	  - metadata & abstracts in YAML frontmatter  

	- **tests/**:  
	  - one test per component (e.g. `test_arxiv_fetcher.py`, `test_text_summarizer.py`, etc.)  

3. **Develop the ArXiv Fetcher**

	- **Test**: Create `tests/test_arxiv_fetcher.py` asserting:  
	  1. Real `arxiv.Search` integration  
	  2. Correct date filtering using prior run files  
	  3. JSON output is written to `output/papers_<date>.json`  

	- **Implementation**: Write `tools/arxiv_fetcher.py` (no placeholders) that:  
	  - Queries ArXiv  
	  - Filters by date  
	  - Writes full JSON output  

	- **Verify**: Run `pytest` and confirm the test passes against the real/mocked API.  


4. **Develop the Text Summarizer**

	- **Test**: Create `tests/test_text_summarizer.py` asserting that any abstract string yields a coherent 1–2 sentence summary.  

	- **Implementation**: In `tools/text_summarizer.py`, call a LangChain LLM (e.g. OpenAI) with a prompt template, handling errors and edge cases—no dummy logic.  

	- **Verify**: Run `pytest` until the summarizer test passes with real LLM calls or properly mocked responses.  


5. **RegistryAgent**  
   - Write `tests/test_registry.py` (discover all `@tool` functions, enforce policies).  
   - Implement `agents/registry.py` with dynamic imports & DAWN‑style checks.

6. **ServiceAgent**  
   - Write `tests/test_service.py` (OAuth flows, error handling, tool invocation).  
   - Implement `agents/service.py` managing real OAuth2 tokens, rate‑limiting, retries.

7. **ExecutorAgent**  
   - Write `tests/test_executor.py` (System 1 vs. System 2 paths).  
   - Implement `agents/executor.py`, calling ServiceAgent and logging CoT.

8. **CriticAgent**  
   - Write `tests/test_critic.py` (relevance & novelty scoring, MAST F1–F14 via Langfuse).  
   - Implement `agents/critic.py` with real span analysis and score logic.

9. **EvaluationAgent**  
   - Write `tests/test_evaluator.py` (Markdown report format, failure tags).  
   - Implement `agents/evaluator.py` to aggregate and format the final report.



*This roadmap unites data science rigor, engineering scalability, and robust governance—powering both cutting‑edge research and personalized, secure agentic services.*  



