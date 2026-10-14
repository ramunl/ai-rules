# Who owns what

The owner's AI agent system: three Telegram bots and a Mini App on one
1 GB VPS. Put a change where the screen, command or data lives; a feature
seen in the dashboard usually needs the dashboard plus the bot that owns the
data.

- **ai-dashboard** – every Mini App screen (Overview, Coding, Tasks, PM, Ops).
  UI-only: reads the bots' snapshot files and calls them through fixed
  bridges. Dragging, buttons, lists and forms in the app live here.
- **ai-pm-agent** – owns the data in `ai-rules` (coding rules) and `ai-todos`
  (todo lists: order, status, priority). Its PM chat commands, the PM snapshot
  and the dashboard bridge `task_cli.py`. A new todo operation goes here first,
  then its screen in ai-dashboard.
- **ai-coding-agent** – plans and implements code changes, tasks, the worker,
  the Coding/Tasks snapshot and the inbox the dashboard writes requests to. A
  new Coding/Tasks action goes here (inbox) and in ai-dashboard
  (`agent_actions.py` and the screen).
- **ai-ops-agent** – server operations: health, logs, services, packages, AI
  tool updates, deployments (`ai-deploy`), backups (`ai-backup`). Root CLIs in
  `deploy/` are also what the dashboard's Ops screen calls.
- **ai-agent-common** – code shared by the three bots (owner check, help,
  version, buttons). Changes ship to every bot via a core release.
- **ai-rules** – coding rules (`global/`, `projects/<name>/`) and this map.
  Data, not code.
- **ai-todos** – todo lists (`projects/<name>/todo.md`). Data, written only by
  ai-pm-agent.
- **channel-cast** (`com.randrgames.channelcast`) – the Android app, separate
  from the agent system.

Order for cross-repo work: data or API first (bot, bridge, CLI), then the
dashboard screen that uses it.
