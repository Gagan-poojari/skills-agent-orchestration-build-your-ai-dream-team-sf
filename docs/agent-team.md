# Agent team

This project uses four custom agents defined in .github/agents/ to build Mona's Project Pulse dashboard.

- Orchestrator — Claude Opus 4.7: coordinates Planner, Coder, and Designer; definition: .github/agents/orchestrator.agent.md
- Planner — Claude Opus 4.7: researches the repo and produces implementation plans, file assignments, dependencies, and validation guidance; definition: .github/agents/planner.agent.md
- Coder — GPT-5.5: implements code, runnable app support, and validation steps (creates launch configs for Project Pulse when needed); definition: .github/agents/coder.agent.md
- Designer — Gemini 3.1 Pro: handles UI/UX, accessibility, visual design, and Project Pulse styling expectations; definition: .github/agents/designer.agent.md

Note: Work is coordinated using the GitHub Copilot CLI inside a Codespace to orchestrate agent tasks and file ownership.