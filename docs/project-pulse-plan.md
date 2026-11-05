# Project Pulse implementation plan

## Summary

Build a polished, responsive static dashboard for viewing project status and
priority. The repository has no app framework or package manifest, so keep the
implementation framework-free and avoid adding dependencies or external APIs
unless later research demonstrates a clear need. Load project records from local
JSON over HTTP, with a predictable local preview setup.

## Phases and file ownership

### 1. Agree on the interface contract — Designer and Coder

Before implementation, Designer and Coder agree on the semantic page structure,
the data shape, and the CSS hooks. At minimum, the page has a main dashboard
region using `.dashboard` and repeated project entries using `.project-card`.
Agree on hooks for status and priority treatments, loading/error/empty states,
and any optional project fields. Keep labels and status meaning available in
text, not color alone.

This contract is a prerequisite for parallel implementation. Do not start the
two implementation tracks until it is settled.

### 2. Implement the page, data, and preview — Coder

**Assigned files:**

- `app/index.html` — Create the accessible semantic dashboard shell, page
  headings, loading and feedback regions, and the script entry point. Render
  records from local JSON; do not hard-code a second source of project data.
- `app/project-data.json` — Define a small, realistic sample set and document
  its expected shape through clear field names and representative values.
  Include enough variation to demonstrate statuses and priority treatments.
- `.vscode/launch.json` — Configure a deterministic browser preview at the
  chosen local URL, opening `index.html` rather than the `app/` directory. Set
  the workspace-relative app root and avoid prompts or variable URLs.

Fetch `project-data.json` relative to the page so it works when served over
HTTP. Show a useful, accessible error message if the request fails or JSON is
invalid; show a distinct empty state for a valid empty list. Handle partial
records without crashing: omit absent optional details, provide safe readable
fallbacks for required display values, and avoid inventing status or priority.

### 3. Implement visual design — Designer

**Assigned file:**

- `app/styles.css` — Style the agreed markup and hooks as a polished dashboard:
  clear hierarchy and typography, readable spacing, visible project cards,
  rounded corners, restrained shadows, strong contrast, and distinct status
  badges and priority treatment. Include coherent loading, error, and empty
  states. Make the layout responsive without hiding essential project
  information on narrow screens.

Designer owns only this stylesheet; Coder owns only the three files listed in
phase 2. If the agreed contract needs to change, coordinate it before changing
markup or selectors rather than editing across ownership boundaries.

### 4. Connect and verify — Coder and Designer

After both tracks are implemented, connect the stylesheet to the agreed hooks
and check the complete dashboard in the configured preview. Coder resolves
data/rendering or preview integration issues in Coder-owned files; Designer
resolves visual issues in `app/styles.css`. Coordinate any contract adjustment
before either changes the shared interface.

## Dependencies and parallelism

- Phase 1 must finish before either implementation track begins.
- Once the contract is agreed, phases 2 and 3 can proceed in parallel because
  their file ownership does not overlap.
- Phase 4 depends on both implementation tracks and their integration.
- Serving over HTTP is required for local JSON fetches; opening the HTML file
  directly is not a supported preview path.

## Local preview task coordination

The existing `.vscode/tasks.json` folder-open task runs
`.devcontainer/postStart.sh`; it is environment setup, not an app runner. Keep
it unchanged. The launch configuration needs a local HTTP server on a fixed
loopback URL (for example, `http://127.0.0.1:4173/`) and should open
`/index.html`.

If no suitable server task already exists, coordinate the smallest necessary
`.vscode/tasks.json` adjustment with its owner: add one named app-preview task
that runs an available standard-library static server, serves only `app/` on
the agreed fixed port, and can be used as the launch configuration's
`preLaunchTask`. Preserve the existing folder-open task and its behavior;
do not replace or repurpose it. Confirm the task's server-start detection and
port agree with `.vscode/launch.json`. Do not add a package manifest or
dependency just to serve these static files.

## Risks and edge cases

- `fetch` may fail when the page is opened as a `file://` URL; use the HTTP
  preview task and make the launch configuration open the file through that
  server.
- Port conflicts or a server that is not ready can make preview startup
  unreliable; use one documented fixed loopback port and ensure launch waits
  for server readiness.
- Treat malformed JSON, request failures, and a valid empty list as distinct
  states with clear user feedback.
- Support missing optional fields and unknown status or priority values
  gracefully; never rely on color alone to communicate meaning.
- On narrow screens, preserve readable text and access to all project details
  rather than forcing a wide table or clipped cards.
- Ensure keyboard users can reach interactive elements, focus is visible, and
  dynamic loading/error feedback is exposed accessibly.
- Keep sample data local and static; do not imply live updates or external
  service availability.

## Validation expectations

- Open the configured preview and confirm it lands on `app/index.html`, loads
  local JSON over HTTP, and leaves the existing folder-open task intact.
- Confirm sample projects render with the agreed `.dashboard` and
  `.project-card` hooks, readable status badges, and clear priority treatment.
- Exercise request failure, invalid JSON, an empty project list, and records
  with optional fields missing; each should produce sensible, non-crashing
  output.
- Check layout at desktop and narrow viewport widths, including that content
  remains readable and is not clipped.
- Navigate with the keyboard and verify visible focus, meaningful labels, and
  accessible loading and error feedback.
- Review contrast and confirm status and priority remain understandable
  without relying solely on color.
