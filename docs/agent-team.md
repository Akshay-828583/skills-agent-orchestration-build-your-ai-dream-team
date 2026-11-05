# Agent team

I will use this custom agent team to build Mona's Project Pulse dashboard:

| Agent | Target model | Responsibility | Definition |
|---|---|---|---|
| **Orchestrator** | Claude Opus 5.5 (copilot) | Coordinates the specialists, divides work into phases with explicit file scopes, manages dependencies, and verifies the integrated result. | `.github/agents/orchestrator.agent.md` |
| **Planner** | Claude Opus 5.5 (copilot) | Researches the repository and produces an implementation plan with steps, file assignments, dependencies, risks, edge cases, and validation expectations. | `.github/agents/planner.agent.md` |
| **Coder** | GPT-5.5 (copilot) | Implements dashboard logic and runnable-app support, following repository patterns and validating the changes. | `.github/agents/coder.agent.md` |
| **Designer** | Gemini 3.7 Flash (copilot) | Shapes the dashboard's visual design, usability, accessibility, responsive behavior, and interaction flow. | `.github/agents/designer.agent.md` |

I am using GitHub Copilot CLI in a Codespace to orchestrate the work.
