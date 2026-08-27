# Project Pulse agent team

I will use GitHub Copilot CLI in a GitHub Codespace to orchestrate a specialist
team that plans, designs, implements, and validates Mona's Project Pulse
dashboard.

| Agent | Model | Responsibility | Definition |
| --- | --- | --- | --- |
| **Orchestrator** | Claude Opus 4.7 (copilot) | Breaks the work into phases, delegates tasks with explicit file ownership, coordinates dependencies and parallel work, verifies the integrated result, and reports the outcome. | `.github/agents/orchestrator.agent.md` |
| **Planner** | Claude Opus 4.7 (copilot) | Researches the repository and requirements, then creates the implementation plan, file assignments, dependencies, ordering, edge cases, and validation expectations. The Planner does not write code. | `.github/agents/planner.agent.md` |
| **Designer** | Gemini 3.1 Pro (copilot) | Owns the dashboard's UX, accessibility, information hierarchy, responsive behavior, visual clarity, project cards, status badges, spacing, and polished styling direction. | `.github/agents/designer.agent.md` |
| **Coder** | GPT-5.5 (copilot) | Implements the assigned static dashboard files, keeps behavior explicit and testable, validates the work, and creates the assigned runnable-app support such as `.vscode/launch.json`. | `.github/agents/coder.agent.md` |

## How the team will work together

The Orchestrator will first ask the Planner to create a plan for Project
Pulse. After reviewing the plan and its file ownership, the Orchestrator will
delegate the experience and accessibility decisions to the Designer and the
implementation of the static HTML, CSS, JSON, and launch configuration to the
Coder. Work will run in parallel only when file scopes and dependencies allow;
otherwise it will proceed sequentially. The Orchestrator will then review the
integrated dashboard, validate it, and summarize the final handoff.

The agents will not stage, commit, or push changes. Git operations remain
learner-controlled through GitHub Copilot CLI prompts.
