# Connection and task recovery

Read this guide only for an observed connection, command, approval or provider blocker. Keep the target and question of the user in view. Explain what prevented the operation. Explain the next useful step. After the repair, continue the investigation.

Commands below use an installed `rea`. If none is available, use the package invocation in `SKILL.md`. Do this only when the user authorizes you to acquire REA. Follow the schemas of the connected server and the help of the installed CLI. Do not assume that repository main and an older release have the same arguments.

## Choose the remedy from the observed failure

Find the observation in this list. Then do the next step.

- No REA tools in the active session:
  - Inspect the registration with `rea doctor --client codex --json`.
  - A skill installation alone does not register MCP.
- Missing, malformed or stale registration:
  - Prepare a scoped setup plan.
  - Apply only authorized changes, with backups.
- Aligned registration, but no connected tools:
  - Restart/reconnect the client.
  - Doctor checks the registration. It cannot establish the current connection of the agent.
  - If reconnection fails, inspect the launch error of the client.
- `MCP tool call requires approval, but approval policy is never`:
  - Codex rejected the call before REA executed it.
  - Do not install an engine or repeat setup to solve this client-policy decision.
  - See the approval section below.
- REA returns `provider_unavailable` or an unavailable tool entry:
  - Read the provider diagnostics of that operation or `binary_session.result.tool_availability`.
  - Expand the tool names of each group.
  - Each group inherits the shared client requirements unless it overrides them.
  - Use the availability, reason, and remediation of the operation.
  - A complete tool catalog does not mean that every operation is available.
- `rea` is absent in a child shell, or Node is incompatible:
  - Inspect the actual shell PATH and the launcher/runtime.
  - A prefix installation can be absent from the PATH of a login shell.
  - This does not establish a broken MCP registration.

If the client cannot launch its shell/sandbox helper or cannot read the installed skill, inspect its launch diagnostic. For example, Codex can refuse helper aliases when CODEX_HOME is under `/tmp`. In that case, use an authorized profile outside the OS temporary directory. REA setup cannot repair the executor of the client. Preserve the sandbox. Do not silently disable it to make an analysis command work.

Static JavaScript and ordinary ZIP inventory need neither Hopper nor Ghidra. HAR inspection is offline. It does not need a live browser. Native decompilation, Android analysis and firmware extraction have their own bring-your-own prerequisites.

Use supported inventory/static evidence when it answers part of the question. Name the unavailable evidence separately. Do not substitute another target. Label observations from ordinary shell tools as such. Only actual REA output supplies REA Evidence.

## Repair registration when needed

For Codex:

```bash
rea doctor --client codex --json
rea setup --client codex --dry-run --json
```

Use the ID of the current supported client: `claude_code`, `claude_desktop`, `codex`, `cursor`, `gemini_cli`, `windsurf`, `devin`, `opencode`, `antigravity`, `copilot_cli`, `commandcode`, `qwen_code`, `vscode`, `trae`, `grok_build`, `omp`, `pi`, `hermes`, `qoder`, `zcode`, `dsh`, or `grok_bot`. If the client is unknown, inspect `doctor --json` before you choose a scope. `grok_bot` uses a chat registration step instead of local `mcp.json`. For unsupported clients, use version-pinned manual stdio registration or CLI.

Show the exact paths, backups, changes and bundled-skill replacement of the plan. If that concrete scope is not already authorized, obtain approval for writes. Then apply the same scope with `rea setup --client codex --yes`.

Add `--install-hopper` only when you separately need it and the user explicitly approves it. Do not install or upgrade unrelated prerequisites. Restart/reconnect. Verify that tools appear. Resume the original task. If tools remain absent, inspect the launch failure. Do not loop through setup.

## Codex tool approval is separate from REA readiness

REA tools have no permission-grant handshake. Codex can still require approval for these reasons:

- An operation creates owned resources.
- An operation launches a helper.
- An operation has cleanup effects.

An advertised tool or a successful doctor result does not override that client policy. `workspace-write` alone does not resolve an MCP approval rejection. `approval_policy = "never"` does not mean that Codex preapproves every MCP tool.

When interactive approval is available, use the approval flow of the client for the requested operation. In an unattended run, do one of these actions:

- Continue through an authorized CLI workflow if the execution environment permits it.
- Report the blocked operation and the approval that you need to proceed.

Do not retry the same rejected call. Do not silently broaden approvals. Do not rewrite MCP effects as read-only.

Current Codex supports per-tool configuration for an operator who explicitly wants to preapprove named operations in a disposable Codex profile. For example, you can scope the opening and closing of an analysis session independently:

```toml
[mcp_servers.rea.tools.open_binary]
approval_mode = "approve"

[mcp_servers.rea.tools.close_binary]
approval_mode = "approve"
```

Use the actual server ID. Use only the operations whose effects the user approved. This example does not approve inspection, extraction or runtime execution. Show the proposed config edit before you write. Preserve unrelated entries and backups. Managed policy can constrain client settings.

Consult the [Codex configuration reference](https://learn.chatgpt.com/docs/config-file/config-reference) for the settings that the installed client supports. This is an optional recovery choice. It is not a prerequisite for every REA task.

## Continue through an installed CLI

Check the command environment only if the launcher fails:

```bash
command -v rea
command -v node
node --version
rea --version
```

When the child shell loses a prefix installation, use the public launcher at its known installation path. Use a supported existing Node on PATH. Avoid internal `dist/cli.js` entrypoints. Avoid global toolchain changes. MCP can have its own valid launcher even when a shell cannot resolve `rea`.

For a JavaScript tree or ASAR that the user supplies, select output files in the authorized workspace. Save complete Evidence before you read a view:

```bash
rea analyze-javascript-application /absolute/path/to/app --json > app-evidence.json
jq -c '{source: {kind: "inline", evidence: .}, view: {kind: "summary"}}' app-evidence.json > app-view.json
rea inspect-analysis-view app-view.json
```

These commands do not register MCP or install an analysis engine. CLI JavaScript JSON is complete and can be large. Redirect it. Do not load it into context. If `jq` is absent, use an existing JSON processor. Do not install one only for this recipe.

Inspect a module page or item with the same inline source and the [JavaScript recipes](javascript-applications.md). Preserve graph coverage, limitations and unknowns. Native CLI analysis still needs its selected engine. All CLI operations keep the execution permissions of the host.
