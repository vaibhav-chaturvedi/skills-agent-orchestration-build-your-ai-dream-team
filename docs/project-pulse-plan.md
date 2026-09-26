# Project Pulse Dashboard Implementation Plan

## Goal and current repository state

Build Mona's Project Pulse as a small, dependency-free static dashboard that lets contributors scan projects, owners, status, recent activity, priority or risk, and a short contributor-friendly summary. The dashboard must open as a web page rather than a directory listing.

The required implementation files are `app/index.html`, `app/styles.css`, `app/project-data.json`, and `.vscode/launch.json`. At planning time, all four are absent. `.vscode/tasks.json` already exists and must be preserved without modification. No app framework or test harness is specified; serve the static app over HTTP so its JSON fetch works.

## Ordered phases and file assignments

| Phase | Owner and assignment | Depends on |
| --- | --- | --- |
| 1. Plan | Planner, coordinated by Orchestrator: document scope, responsibilities, dependencies, parallel work, risks, and validation in `docs/project-pulse-plan.md`. | Repository brief and agent definitions. |
| 2. Agree contracts | Orchestrator with Designer and Coder input: agree on page hierarchy, CSS hooks, project data shape, and launch behavior. No implementation files change in this phase. | Phase 1. |
| 3. Parallel foundation work | Designer owns `app/styles.css`. Coder owns `app/project-data.json` and `.vscode/launch.json`. Keep the file ownership separate and preserve `.vscode/tasks.json`. | Phase 2 contracts. |
| 4. Page implementation | Coder owns `app/index.html`: implement the accessible page, connect the stylesheet and JSON, and render the project cards. | Phase 2 contracts and the agreed CSS hooks/data shape from Phase 3. |
| 5. Integration review | Orchestrator reviews the complete dashboard with Designer and Coder support; resolve issues within the relevant specialist's scope. | Phases 3 and 4. |

## Responsibilities and file requirements

- **Designer — `app/styles.css`:** establish clear dashboard hierarchy, readable spacing, accessible contrast, status and priority treatments, and a responsive layout. Include `.dashboard` and `.project-card` hooks, rounded-card styling with `border-radius`, and depth with `box-shadow`. Coordinate hooks with Coder; do not edit `app/index.html`.
- **Coder — `app/project-data.json`:** provide a top-level `projects` array. Each project includes `name`, `owner`, `status`, `recentActivity`, `priority`, and a short contributor-friendly `summary`. No authoritative project records were supplied; confirm whether approved records are available, otherwise clearly label the records as illustrative samples.
- **Coder — `app/index.html`:** use the exact page title `Project Pulse`; link `styles.css` and load `project-data.json` from the served app. Render a `.project-card` per project and visibly show the name, owner, status, recent activity, priority, and summary. Provide explicit loading, fetch-failure, and empty-data states. Treat data as untrusted: render values as text, guard missing or unexpected fields, and provide understandable treatment for unknown status or priority values.
- **Coder — `.vscode/launch.json`:** create strict JSON with no comments and a configuration named `Run Project Pulse Dashboard`. Set `cwd` to `${workspaceFolder}/app`; serve using `python3 -m http.server 5500`; configure `serverReadyAction` to open `http://localhost:%s/index.html` when ready. Open `index.html`, not the directory root. Do not edit or replace `.vscode/tasks.json`.
- **Orchestrator:** coordinate phases, confirm agreements, prevent overlapping edits, and verify the integrated experience. Coordinate implementation but do not implement the dashboard.

## Dependencies and parallel work

Complete Phase 1 before Phase 2. Once the page and data contracts are agreed in Phase 2, the Designer can implement CSS in parallel with the Coder's JSON data and launch configuration: those file scopes do not overlap, and launch setup does not depend on visual design. Implement HTML after CSS hooks and the data shape are settled. Finish with an integration review after all implementation files are ready.

Keep the app static and dependency-free. `file://` loading does not reliably support fetching the JSON file, so runtime validation must use the HTTP server. The launch configuration's fixed port may already be in use; Python or the relevant VS Code debugger support may be unavailable, and a server that fails to stop must be surfaced rather than ignored.

## Risks and edge cases

- Sample data is illustrative unless Mona supplies approved project records.
- Empty data and fetch failures need visible, explicit UI states.
- Missing fields, unexpected values, and unknown status or priority values must not break rendering.
- Status and priority must not be communicated by color alone; maintain readable contrast and a usable narrow-screen layout.
- Render project data as text to avoid interpreting untrusted values as markup.
- Serve over HTTP to avoid `file://` fetch restrictions, and report port conflicts or missing runtime/debugger support.

## Validation expectations

### Plan and workflow checks

- Confirm this plan includes Project Pulse, Designer and Coder responsibilities, all four required file paths, dependencies, parallel work decisions, and validation expectations.
- Step 2 (`.github/workflows/2-step.yml`) runs on manual dispatch or a push to `main` affecting `docs/project-pulse-plan.md` (excluding branch-creation pushes and the template initial commit). It checks that this plan exists and contains case-insensitive key phrases for Project Pulse, Designer, Coder, each required file path, dependencies, parallel work, and validation. These are presence/phrase checks, not a semantic review of the plan.

### Implementation structure and wiring

- Confirm all four assigned implementation files exist and `.vscode/tasks.json` remains unchanged.
- Parse `app/project-data.json`; verify the top-level `projects` array and every required field, including `summary`.
- In `app/index.html`, verify the exact `Project Pulse` title, stylesheet and JSON wiring, and visible rendering of project name, owner, status, recent activity, priority, and summary; exercise loading, empty, and fetch-failure states.
- In `app/styles.css`, verify `.dashboard`, `.project-card`, `border-radius`, and `box-shadow`, plus responsive, readable styling and accessible contrast.
- Parse `.vscode/launch.json` as strict JSON and verify the exact configuration name, `cwd`, server command/port, and ready URL target.

### Repository Step 3 workflow checks

- Step 3 (`.github/workflows/3-step.yml`) runs on manual dispatch or a push to `main` affecting `app/**` or `.vscode/launch.json` (excluding branch-creation pushes and the template initial commit). It checks that the four implementation files exist; checks case-insensitive key phrases in the HTML, CSS, data, and launch files; and parses both JSON files with `python3 -m json.tool`.
- The workflow checks phrases such as `Project Pulse`, asset names, `project-card`, visible field names, CSS hooks and treatments, project data field names, `Run Project Pulse Dashboard`, and `index.html`. These checks do not prove that data has the required structure, that content renders correctly, or that the launch command, working directory, and ready URL behave correctly. In particular, the launch checks cover JSON syntax, configuration-name text, and `index.html` text—not runtime behavior or exact command/URL configuration.
- Run the dashboard through the VS Code configuration, confirm the browser shows `index.html` rather than a directory listing and that cards load from JSON, exercise empty and fetch-failure states, then stop the server. This runtime review complements the automated workflow checks; the workflow does not start an app or test a browser.

### Exercise-template validation

`scripts/validate-exercise.sh` validates exercise scaffolding, workflow definitions, and related repository conventions. It also expects learner answer files—including this plan and the app outputs—not to be tracked in the exercise template. It is not completed-submission validation: it does not validate the dashboard's runtime or the plan's implementation correctness.
