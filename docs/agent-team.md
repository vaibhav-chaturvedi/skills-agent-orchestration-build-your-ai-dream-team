# Agent team

The Mona's Project Pulse dashboard team is orchestrated through **GitHub Copilot CLI in a Codespace**:

| Agent | Target model | Responsibility | Definition |
|---|---|---|---|
| **Orchestrator** | Claude Opus 4.7 (copilot) | Coordinates the specialists, breaks the work into phases, manages file ownership and dependencies, and verifies the integrated result. | `.github/agents/orchestrator.agent.md` |
| **Planner** | Claude Opus 4.7 (copilot) | Researches the repository, documentation, dependencies, risks, edge cases, and validation needs, then produces the implementation plan. Planner does not write code. | `.github/agents/planner.agent.md` |
| **Designer** | Gemini 3.1 Pro (copilot) | Defines and implements the Project Pulse dashboard's UI/UX, accessibility, information hierarchy, interaction flow, responsive behavior, and visual styling. | `.github/agents/designer.agent.md` |
| **Coder** | GPT-5.5 (copilot) | Implements the dashboard logic and runnable-app support with clear, deterministic, testable code; validates the change and reports remaining risks. | `.github/agents/coder.agent.md` |

The Orchestrator first obtains the Planner's implementation strategy, then assigns non-overlapping work to the Designer and Coder as dependencies allow, and finally verifies that the completed dashboard works as one integrated experience. None of the custom agents stages, commits, or pushes changes; Git operations remain under the learner's control through Copilot CLI.
