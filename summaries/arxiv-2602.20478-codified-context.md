# Codified Context: Infrastructure for AI Agents in a Complex Codebase

**Paper:** [arXiv:2602.20478](https://arxiv.org/abs/2602.20478)
**Author:** Aristidis Vasilopoulos (Independent Researcher, USA)
**Subject:** Software Engineering (cs.SE)
**Pages:** 9 pages, 4 figures, with companion repository

---

## Problem

LLM-based agentic coding assistants lack persistent memory: they lose coherence across sessions, forget project conventions, and repeat known mistakes. Recent studies characterize how developers configure agents through manifest files (e.g., CLAUDE.md, .cursorrules), but an open challenge remains — how to scale such configurations for large, multi-agent projects.

## Three-Component Infrastructure

The paper presents a **codified context infrastructure** developed during construction of a 108,000-line C# distributed system, consisting of three tiers:

1. **Hot-memory constitution (Tier 1):** A concise, always-loaded document encoding conventions, retrieval hooks, and orchestration protocols. Designed to fit entirely in every session without excessive context window consumption. Answers: *"What rules must you always follow?"*

2. **19 specialized domain-expert agents (Tier 2):** Each agent embeds project-specific knowledge for a particular domain (e.g., save systems, networking, UI). Trigger tables enable automatic routing — removing the burden of the developer remembering which agent to invoke.

3. **Cold-memory knowledge base (Tier 3):** 34 on-demand specification documents providing detailed subsystem documentation. Referenced by link from the constitution. Answers: *"How does subsystem X work in detail?"*

## Architecture

- **Trigger Tables:** Encode institutional knowledge about which domain expertise each file area requires, enabling automatic task routing.
- **MCP Retrieval Server:** Uses the Model Context Protocol to automate task routing and provide on-demand access to project-specific specifications from the cold-memory knowledge base.
- **Hot/Cold Memory Separation:** Always-loaded conventions (hot) vs. on-demand specifications (cold) — balancing context window efficiency with comprehensive knowledge access.

## Quantitative Findings

Evaluation across **283 development sessions**:

| Metric | Value |
|--------|-------|
| Human prompts | 2,801 |
| Agent invocations | 1,197 |
| Agent turns | 16,522 |
| MCP retrieval calls | 1,478 (across 218 sessions) |
| Agent conversations reading KB docs | 194 |

The knowledge base was actively used throughout development, demonstrating sustained reliance on codified context rather than diminishing usage over time.

## Case Studies

Four observational case studies illustrate distinct roles of codified context:

1. **Coordination:** Multi-agent coordination across subsystem boundaries
2. **Experience capture:** Encoding past failures to prevent recurrence
3. **Gap detection:** Identifying missing documentation that leads to agent errors
4. **Domain-expert diagnosis:** Specialized agents diagnosing issues within their domain (e.g., a save system using two-tier architecture where writing to the wrong tier causes subtle data corruption)

## Key Contributions

- Treats **documentation as infrastructure** — load-bearing artifacts that AI agents depend on, not optional reference material
- Complements multi-agent coordination frameworks: while those define *how* agents coordinate, this work structures the *knowledge* agents depend on
- Indexes **knowledge about code** (design intent, constraints, failure modes) rather than code itself
- Provides an **open-source framework** with representative agent specifications, MCP retrieval server, example documents, factory agents for bootstrapping, and analysis scripts

## Relevance to This Repository

This paper's approach aligns directly with the Context Engineering patterns in this repository — particularly the use of CLAUDE.md as a "hot-memory constitution," specialized agent protocols, and structured knowledge retrieval for maintaining coherence across AI-assisted development sessions.

---

*Summary generated on 2026-03-01*
