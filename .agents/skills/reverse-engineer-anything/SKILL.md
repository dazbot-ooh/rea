---
name: reverse-engineer-anything
description: Use REA to reverse engineer native, managed, Electron/JavaScript, packaged, firmware, and browser targets. Use shipped-artifact evidence or requested runtime evidence to explain features, compare versions, decompile code, or guide a reconstruction. Do not use REA for ordinary architecture analysis of a source repository.
metadata:
  version: "40"
---

# REA

Use REA when a claim depends on one of these items:

- A shipped binary or package.
- Decompilation.
- Passive application runtime evidence.
- A comparison with behavior that the available source does not establish.

For ordinary analysis of a complete source repository, use normal repository tools and do not run REA
readiness or provider commands.

## Connect only when needed

This skill supplies instructions. It does not register the REA MCP
server. It does not install analysis engines. It does not add tools to an agent that already runs.

If REA tools are available, and you do not know that their registration is stale,
go directly to the target. Do not run diagnostics or setup before every
investigation. Use the actual tool list and input schemas of the connected server.
A skill that you install from repository main can describe capabilities that an
older npm release does not have. Keep complete inline Evidence when the server does not
advertise retained references.

Read [connection and recovery](references/connection-and-recovery.md) in these cases:

- Tools are absent.
- The client blocks a call.
- A required provider is unavailable.

Diagnose the blocker that you observe. Continue the useful supported work. After the repair, resume the
original task. Client approval, shell PATH, registration, and engine
availability have different remedies. Do not repeat setup for all of them.

Prefer an existing `rea` CLI. If you find no `rea` CLI, and the authorized scope includes
REA package acquisition, use `npx -y rea-agents@latest` in its place.

Configuration repair starts with a read-only setup plan. Show paths, backups, and
skill replacement before you apply authorized changes. Preserve existing approval
and target-execution boundaries. REA setup never installs or upgrades unrelated
software. This includes Node.js, npm, Homebrew, Java, Ghidra, IDA, JADX, Binwalk, and Unblob.

## Route the target first

Choose the first tool from the target that the user supplied. Use `open_binary` for
active-target native or archive workflows. Target-free tools take their own
explicit path or endpoint. They do not need `open_binary`.

- ASAR or extracted JavaScript/Electron tree:
  `analyze_javascript_application`.
- Archive/package member inventory (ZIP/APK/IPA/MSIX/AppX or DMG):
  `open_binary` with the supplied local path. Use `inspect_artifact` when its
  graph and findings help to answer the question.
- Android APK code, classes, methods, or incoming references:
  `inspect_android_package`, then focused Android tools when the server advertises them.
  Archive member inventory still uses the archive route above.
- Managed PE/CLI assembly: `inspect_managed_artifact`.
- Firmware image: `inspect_firmware_regions` when the server advertises it. If the user requests extraction, use
  `extract_firmware` with a new absolute output directory that the caller selects. These tools use Binwalk/Unblob that the caller supplies on
  Linux. See the [firmware guide](https://github.com/morluto/rea/blob/main/docs/firmware-analysis.md).
- .NET NativeAOT PE/ELF: native Ghidra analysis. Native code can yield pseudocode.
  For optional metadata recovery and its supported host/layout boundary, consult the NativeAOT workflow in `references/native-and-artifacts.md`.
- User-owned browser page already open: `list_browser_targets`.
- Retained HAR/native mitmproxy capture: `inspect_web_network_capture`. When it is useful, select
  original record ordinals. Inspect producer fields and byte/number
  sidecars. Do not fetch recorded URLs. Do not claim live attribution. For the exact upstream
  profile and credential exclusions, see
  [historical captures](https://github.com/morluto/rea/blob/main/docs/web-network-captures.md).
- User-owned Electron runtime already open: `list_electron_targets`.
- Recorded packet captures: `inspect_packet_capture` with selected TShark fields,
  display filters, and decode-as rules. It requires Linux, util-linux prlimit, and `REA_TSHARK_COMMAND`. See the
  [capture guide](https://github.com/morluto/rea/blob/main/docs/packet-captures.md).
  Packet limits count input packets before filtering. Output defaults to 50 matching
  frames. Use `match_offset` / `match_limit` for later pages. For complete matching output, use an explicit null
  limit. Preserve the source digest across pages.
  This does not perform live capture.
- Recorded logic/signal traces: `decode_signal_recording` with explicit decoder,
  channel, and protocol settings. On Linux with util-linux prlimit, supply sigrok-cli 0.7.2 through
  `REA_SIGROK_COMMAND`. See the [recording guide](https://github.com/morluto/rea/blob/main/docs/signal-recordings.md).
  Output defaults to 50 literal lines. Select annotation classes. Page with
  `line_offset` / `line_limit`. For all remaining output, use an explicit null.
  Window counts are display lines. They are not annotations or samples. Preserve source digests across pages.
  This reads existing files. It does not acquire hardware signals.
- Explicit local WebAssembly artifact: `inspect_wasm_artifact`. Supply WABT 1.0.42
  through `REA_WABT_BIN_DIRECTORY`. Retain exact byte/tool identity and decoded WAT.
  Select JavaScript glue and candidate paths explicitly. Preserve URL/path
  ambiguity. There is no implicit fetch or target execution. WAT is not original source.
  See the [WASM guide](https://github.com/morluto/rea/blob/main/docs/wasm-artifacts.md).
- Explicit local EVM bytecode carrier: `inspect_evm_interface` with `raw` or `hex` encoding
  that the caller selects. Preserve carrier/decoded digests. Treat selectors,
  argument strings, and mutability as inferred candidates. This performs no chain
  lookup or target execution. To get the complete inferred interface without hex or
  duplicate stdout, discover support and select `detail: "summary"`. Complete
  Evidence stays retained under its original identity. Use the returned
  `parent_evidence_id` for exact function or `bytecode_hex`/stdout/stderr pages.
  Candidate coverage does not establish interface completeness. See the
  [EVM bytecode guide](https://github.com/morluto/rea/blob/main/docs/evm-bytecode.md).
- Embedded Go compiler, module, or build-setting metadata: `inspect_go_binary`
  with the explicit local file. This built-in target-free inspection does not need a
  Go installation or a disassembler. Preserve raw module bytes and source ranges.
  Absent metadata does not prove that a binary is not Go. Unparsed text records
  remain explicit. See the
  [Go binary guide](https://github.com/morluto/rea/blob/main/docs/go-binaries.md).

- Explicit local SQLite database snapshot: `inspect_sqlite_database`. Inspect
  with Node.js 24.x >=24.15 or 26+ that provides native SQLite limits. Read the schema
  first (compact inventory by default). When you need rows, select an exact ordinary table
  and `columns`. The defaults give the schema of the selected table and
  20 rows. For the complete stored schema, request `schema_scope: "database"`. Preserve both the database identity and the identity of an existing sibling WAL. Row samples
  report truncation and have unspecified order. Use a quiescent snapshot. Do not
  assume that the copy of a live database/WAL pair is atomic. See the
  [SQLite guide](https://github.com/morluto/rea/blob/main/docs/sqlite-databases.md).
- Explicit Linux ELF file for offline layout, symbols, relocations, or static
  mitigation evidence: `inspect_binary_layout`. This target-free operation uses
  pwntools that the caller supplies. It does not open a disassembler database. Preserve its
  raw locations and inference/coverage limits. See the
  [offline binary guide](https://github.com/morluto/rea/blob/main/docs/binary-diagnostics.md).
- Supplied Linux x86-64 ELF core: `inspect_recorded_crash`. Read every recorded
  thread, raw note, and source-bound register. Do not open a live target.
  Historical PIDs do not select live processes. Request `include_debugger_context`
  only when core-only mapping candidates help. See the
  [recorded crash guide](https://github.com/morluto/rea/blob/main/docs/recorded-crashes.md).
- PE resource type/name/language trees and static icon group references:
  `inspect_pe_resources` with an explicit path. This portable operation needs
  no active target or engine. Opaque payloads retain file ranges and SHA-256.
- Native executable, library, or analysis database: `open_binary`, then
  use focused analysis tools directly. Call `binary_overview` when metadata or
  inventory context is useful and the selected provider makes it available.

For an existing IDA MCP configuration, use `open_binary` with the original
input binary and `provider_id: "ida"`. Do not pass an `.idb` or `.i64` database.
The legacy attached profile binds the GUI input that is already open by SHA-256. It
leaves its database open. The modern headless profile creates an
owned private database and closes it without a save.

Installation and registration reuse
[mrexodia/ida-pro-mcp](https://github.com/mrexodia/ida-pro-mcp). See the
[IDA provider guide](https://github.com/morluto/rea/blob/main/docs/ida-provider.md).

After you open the binary, go directly to `analyze_function`, pseudocode, or function/string searches.
IDA does not supply `binary_overview`. Consult session availability
when you compose broader workflows. Modern direct callers and unsupported dossier
facets remain unknown. REA does not replay live IDA results from snapshots.

Sometimes the user says only "my app". If the user gives no name and no path, and the task has no selected target, ask which app before you search for artifacts. Paths in unrelated
saved reports and nearby test fixtures do not select the target of the user. Resolve
an actual human-readable app name to one clear installed artifact when possible.
Ask when the matches are ambiguous. Never choose an example app for the user.

In a target-free session, use `open_binary` to bind any archive/package or
native target whose analysis tool operates on the active target. Do not call a tool
that the server does not advertise.

Inspect `binary_session` with `{}` and its
`result.tool_availability` for availability reasons and remediation. Expand the `names` of each
group into entries. The entries inherit the client requirements of the inventory level,
unless the group overrides them. In its closed effect and annotation vocabularies,
the listed flags are true and the omitted known flags are false. The union of the grouped names
is the complete tool inventory. Use `tools/list` for canonical tool schemas.

Provider candidate facts describe possible providers. Top-level session
capabilities describe the current route. `analysis_provider_binding` names the
selected provider. Do not treat those scopes as interchangeable.

## Work summary-first

Start with the default result. Use its inline Evidence and graph context.
Sometimes the size of a JavaScript application or ELF layout is unknown or large. In this case,
call `analyze_javascript_application` or `inspect_binary_layout` with
`"detail": "summary"`. Then inspect the retained `parent_evidence_id` with
`inspect_analysis_view`.

Page module identities. Select the returned `node_id` before you request an
item. A single file path can represent several graph roles. Do not repeat an
identical analysis or a rejected call, unless the input changes or you resolve the blocker.

Recheck session state when a lifecycle change makes that status useful.
Make a focused follow-up only when the
returned result leaves a specific question unanswered.

If MCP reports `resource_constraint` with `details.resource: "transport"`, call
`inspect_analysis_view` with its reported same-session Evidence reference. Request a
summary, one section/module, or a stable page. Use `trace_application_feature`
when you know a seed.

Alternatively, call `export_evidence_bundle` to write the complete
session to a selected path. The analysis remains complete in the ledger. Do not
repeat it only to request the same oversized response. Complete CLI JSON
output also streams without a single MCP frame.

When the retained parent ID is unknown, use `get_evidence_bundle` with
`detail: "summary"` and exact filters for these items:

- Operation.
- Target SHA-256.
- Analysis-profile digest.
- Canonical procedure address.

Select its returned Evidence ID for the next view. Do not request the complete bundle.
`retention: "complete-record"` means that REA stores the whole record. Native
value-flow truncation and unknown metadata remain separate. For an exported
portable bundle, use `rea inspect-evidence-bundle` with JSON that contains `path`,
`detail: "summary"`, and the same `filters`. To read that file, you must still
validate its full contents within the local memory budget.

A client can also truncate a successful tool result without a REA transport
error. Treat that preview as incomplete. When you need the full record, use the retained parent ID for smaller
views or use export.

Do not parse the preview as
canonical JSON. Do not repeat the producer.

For JavaScript, read the
[module-view and trace recipes](references/javascript-applications.md) before
you drill into a large graph. A broad bidirectional trace can expand unrelated
containment relationships, even after summary analysis.

Every conclusion must distinguish observations, inferences, and unknowns. Cite
Evidence IDs. Preserve limitations and incomplete coverage. Never imply
that static analysis observed execution. Runtime requests execute the declared
target and lifecycle. Do not broaden the target or action beyond those fields.

Within the investigation that the user requests, call available analysis tools
directly. REA does not require permission grants or per-call approval flags.
The agent client can independently require approval. Follow the declared request
scope and the actual access requirements of the host. For an observed client-policy rejection, use the recovery guide.

## Plan broader investigations

For requests that span multiple features or subsystems, use a staged workflow:

1. Turn the request into a checklist of questions and the evidence that each answer
   needs. Resolve target identity and constraints from the conversation and
   workspace before you ask for information again.
2. Inspect the current REA session, artifact identity, saved analysis database,
   bookmarks, and prior evidence. Reuse matching state. Do not open duplicate
   sessions. Do not repeat identical analysis.
3. Start with the smallest useful overview or inventory. Follow each question
   from its entry point through relevant data and state changes to its result.
   Batch related operations around a specific hypothesis. Expand only when
   the returned evidence leaves a concrete gap.
4. Inspect relevant packaged resources and configuration alongside code when
   they affect the question. Use format-aware inventory and parsers. Do not
   infer behavior from filenames, strings, or layout alone.
5. Corroborate a conclusion with the evidence type that it requires. Use runtime
   observation only when static evidence cannot answer the question and the
   required host runtime and OS access are available. For JavaScript behavior,
   run probes against the actual app. Use the available browser, Electron,
   or process capture workflow.
6. Decompose work into independent questions. Sometimes parallel workers are
   available and the questions do not depend on one another. In this case, assign distinct
   scopes and point workers to existing evidence. Ask them to return sources,
   conclusions, and unresolved gaps. Otherwise, work sequentially.
7. Keep a concise finding ledger. Link each conclusion to Evidence IDs,
   confidence/evidence type, search boundary, and remaining unknowns. Update
   the shared index or investigation report. Then later passes can reuse the results.

Before you finish, revisit the original checklist. Mark each question as
answered, partially answered, or unresolved, based on its evidence. Keep
bounded negative searches bounded. Do not describe a broad investigation
as complete while required questions remain open.

## Read only the relevant guide

- Native binaries, managed assemblies, archives, and extraction:
  [references/native-and-artifacts.md](references/native-and-artifacts.md)
- ASARs, extracted JavaScript, feature tracing, and version comparison:
  [references/javascript-applications.md](references/javascript-applications.md)
- Android APK declarations, classes, methods, and static references:
  [references/android-applications.md](references/android-applications.md)
- Passive browser/Electron observation and static/runtime reconciliation:
  [references/runtime-observation.md](references/runtime-observation.md)
- Evidence paging, comparisons, residual unknowns, and verification:
  [references/evidence-workflows.md](references/evidence-workflows.md)

## Finish the task

Explain findings in plain language. Tie them to returned evidence. When the
user asks you to build something, use normal coding tools. Separate observed
behavior from design choices. When the investigation is complete, close an opened native session with
`close_binary`.
