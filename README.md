# Flowable Atlas

Map **any** Flowable project (app models **+** Java code) into:

- 🧭 **`<project>.explorer.html`** — a self-contained, offline, interactive explorer (open by double-click). Browse processes, cases, forms, services, data objects, REST endpoints, Java delegates/bots/listeners, user groups — and click through every relationship in both directions.
- ⚡ **`<project>.summary.md`** — a compact (~few KB) LLM-first overview: apps, inventory, entry points, integrations, hotspots, external surface.
- 📄 **`<project>.overview.md`** — the full human/LLM Markdown report (every model, relationship and the access map).
- 🕸️ **`<project>.graph.json`** — the full traversable model↔code graph for agents/LLMs to query.

No dependencies — just **Python 3**. Works on a project directory, on loose model files, and on exported `.zip` / `.bar` archives.

The HTML explorer links each process's service tasks straight to the Java class & method (e.g. `${myService.doWork(...)}` → `MyService.doWork()`), and lets you click through every relationship in both directions.

## Quick start

```bash
./atlas /path/to/your-flowable-project
```

That analyzes the project, writes all four artifacts to `./atlas-output/<project>/`, and opens the HTML explorer in your browser.

Optional output directory and flags:

```bash
./atlas /path/to/project ./reports          # write into ./reports
./atlas /path/to/app.zip --no-open           # analyze an exported archive, don't auto-open
```

> First run not executable? `chmod +x atlas`

## What it understands

It discovers models (`.bpmn`, `.cmmn`, `.dmn`, `.form`, `.app`, `.service`, `.data`, `.agent`, `.action`, `.sequence`, …) — loose **and** inside `.zip`/`.bar` — plus `.java` and Liquibase changelogs, then resolves the relationships between them:

- App → its models · process/case → called process/case/decision/form
- `${bean.method()}` / `delegateExpression` / listeners → the **Java class & method** (with `file:line`)
- form / process → **REST endpoint** → the serving **controller**
- action → **bot** (the Java `BotService` class) · agent → tools
- data object → backing **service**, columns & **Liquibase** table · service → Liquibase (`referencedLiquibaseModelKey` + `tableName`)
- **who can do what** — candidate (starter) groups, app/page access, data-object identity links, security policies
- sequences → the cases/processes that use them · Java → Java (dependency injection)

Whatever can't be resolved in the project (Flowable platform beans, external REST) is listed separately so real gaps stand out.

## Advanced — single artifacts

```bash
python3 flowable_atlas.py <project> --summary --stdout     # compact overview to stdout
python3 flowable_atlas.py <project> --html  -o explorer.html
python3 flowable_atlas.py <project> --json  -o graph.json
python3 flowable_atlas.py <project>                         # full Markdown report (default)
python3 flowable_atlas.py <project> --all   -o ./out        # all four (what ./atlas does)
```

## For LLMs / agents

- Start with **`--summary`** (small, high-signal) as first context.
- Then have the agent load **`--json`** and traverse the `graph` (`nodes` + `edges`) to answer specific questions, instead of pasting the whole report.

## Requirements

Python 3.8+. No third-party packages.
