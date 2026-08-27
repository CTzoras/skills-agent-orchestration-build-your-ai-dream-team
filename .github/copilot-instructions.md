# Copilot instructions

## Repository purpose

This repository is a GitHub Skills exercise, not a production application. Learners use GitHub Copilot CLI in a GitHub Codespace to orchestrate a prebuilt team of custom agents and build Mona's static **Project Pulse** dashboard.

The exercise progresses through four learner-owned outputs:

1. `docs/agent-team.md` documents the agent team.
2. `docs/project-pulse-plan.md` records the Planner's implementation plan.
3. `app/index.html`, `app/styles.css`, `app/project-data.json`, and `.vscode/launch.json` implement and preview the dashboard.
4. `docs/final-handoff.md` records validation and the Orchestrator's handoff.

Do not add learner outputs to the template unless the task explicitly asks for them; the exercise workflows expect learners to create these files later.

## Build, test, and lint

There is no package manager, application build, test framework, or linter. The repository's validation entry point is:

```bash
bash scripts/validate-exercise.sh
```

For targeted checks, use the same commands used by the validator:

```bash
python3 -m json.tool .devcontainer/devcontainer.json
python3 -m json.tool .vscode/tasks.json
python3 -m json.tool app/project-data.json
python3 -m json.tool .vscode/launch.json
bash -n .devcontainer/postCreate.sh
bash -n .devcontainer/postStart.sh
```

The last two JSON commands apply after the learner creates the corresponding files. There is no single-test selector because no test suite exists; validate a changed workflow or artifact with the relevant command above, then run the full script before completing repository-level changes.

## Architecture and workflow

- `.devcontainer/devcontainer.json` uses the Universal dev container, waits for `.devcontainer/postCreate.sh`, configures terminal ergonomics, and installs the Copilot and GitHub Actions VS Code extensions.
- `.devcontainer/postCreate.sh` verifies `gh`, installs GitHub Copilot CLI into `$HOME/.local/bin` when needed, smoke-tests `copilot --version`, and creates only the initial `docs/agent-team.md` starter.
- `.devcontainer/postStart.sh` starts `copilot --allow-all --enable-all-github-mcp-tools` in the integrated terminal. `/login` is used inside Copilot CLI when authentication is requested.
- `.vscode/tasks.json` automatically opens that terminal task when the folder opens.
- `.github/agents/*.agent.md` defines the reusable Orchestrator, Planner, Designer, and Coder roles. The Orchestrator coordinates; Planner plans without coding; Designer owns UX/accessibility direction; Coder owns implementation and runnable-app support.
- `.github/project-pulse-brief.md` is the source of truth for the dashboard requirements. It defines the static app, its project fields, the card-based UI, and the launch behavior.
- `.github/steps/*.md` are learner instructions. `.github/workflows/1-step.yml` through `4-step.yml` gate progression by watching the expected output paths and checking required phrases/files. `0-start-exercise.yml` starts the exercise issue and posts step 1.

The intended orchestration is sequential where outputs are dependencies: inspect agents, ask the Orchestrator to obtain a Planner plan, then delegate Designer and Coder work from that plan, then have the Orchestrator validate and write the handoff. Design guidance and independent implementation preparation may be parallel only when file ownership and dependencies do not overlap.

## Repository-specific conventions

- Use GitHub Copilot CLI as the primary orchestration interface in Codespaces. Select the Orchestrator with `/agent` for multi-agent work instead of issuing one undifferentiated implementation prompt.
- Preserve explicit file ownership. Planner plans; Designer guides UI/UX; Coder implements assigned files; Orchestrator coordinates and verifies. Agents must not stage, commit, or push—the learner performs Git operations through Copilot CLI prompts.
- Keep the Project Pulse app static and dependency-free: `index.html` loads `styles.css` and `project-data.json`, renders visible elements using the `project-card` class, and exposes each project's `status`, `recentActivity`, and `priority`.
- Project data must be valid JSON with a top-level `projects` array; each project includes `name`, `owner`, `status`, `recentActivity`, and `priority`.
- Dashboard styling must include `.dashboard` and `.project-card`, plus rounded cards (`border-radius`), shadows (`box-shadow`), readable hierarchy, accessibility, and responsive layout.
- `.vscode/launch.json` must be strict JSON with no comments. The `Run Project Pulse Dashboard` configuration serves from `${workspaceFolder}/app` using `python3 -m http.server 5500` and opens `http://localhost:%s/index.html`, not a directory listing.
- Keep workflow checks deterministic and aligned with the existing action conventions: use `skills/action-keyphrase-checker@v2` for phrase checks with `case-sensitive: false`, and `skills/exercise-toolkit/actions/file-exists@v0.9.3` for file existence checks.
- Preserve workflow guards that skip branch-creation pushes and the template's `Initial commit`; keep step path filters aligned with the learner artifact each workflow validates.
- Do not replace prebuilt agent definitions or the exercise's starter content when making unrelated changes. Update documentation only when it directly reflects the repository's current exercise behavior.
