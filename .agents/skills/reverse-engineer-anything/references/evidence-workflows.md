# Evidence, comparison, and verification workflows

Evidence-producing tools return the complete Evidence record directly in MCP `structuredContent` and CLI JSON. Read `normalized_result` for operation data. Cite `evidence_id` when another tool or your explanation needs a stable reference. Preserve authority, limitations, coverage, and residual unknowns.

If a result is incomplete or paginated, continue only when the remaining data matters to the task. Do not fetch a bundle or resource only to read a result that REA already returned inline.

When complete `inspect_binary_layout` or `analyze_javascript_application` Evidence is already retained, call `inspect_analysis_view`. Do not repeat the producer. Use `inspect_analysis_view` to get one of these items:

- a summary
- one section, symbol, or module
- a stable page

Completed `inspect_artifact` inventory also supports a summary and `view: {kind: "inventory", collection: "occurrences", logical_paths: ["package.json", "main.js"]}`. Omit `logical_paths` to page all available occurrences. `[]` selects none. The other inventory collections are `nodes`, `edges` and `integrity_contradictions`.

Keep `source_complete` separate from page exhaustion. A missing match in partial source Evidence does not establish absence. Use the returned parent Evidence ID for subsequent views. Export before you close the session when the source must survive it.

When `tools/list` advertises `inspect_artifact.detail`, request `"summary"`. This request retains the complete source and returns that view immediately. Without that advertised choice, keep the existing complete call. CLI inspection returns portable complete Evidence. Save it before you project a selected inline view.

Use `record_unknown` to track unresolved questions in the investigation of the user. Name the authority or environment that you still need. Supply supporting and contradicting evidence IDs. It updates the session registry directly.

Use `update_unknown` with the current revision. After a stale revision, reread. Do not retry blindly. Only qualifying observed evidence can verify a resolution.

Use comparisons with complete, compatible page sets when you claim equivalence or absence:

- `compare_artifacts` compares inventory pages by occurrence path, content, metadata, and graph relations.
- `compare_functions` compares explicit function Evidence. It does not make fuzzy whole-binary matches.
- `compare_bundles` compares canonical bundle membership and unknown history.
- `find_changed_behavior` combines existing comparisons. Static differences remain candidates. They are not causal proof.
- `build_call_path` needs explicit function dossiers from one artifact/provider. An incomplete frontier makes absence unknown.
- `correlate_static_and_runtime` uses explicit mappings. Matching patterns are hypotheses. They are not causality.
- `verify_reconstruction` evaluates a finite typed specification. A pass covers only the declared comparable claims. It does not cover global equivalence.

Process captures are opt-in behavioral evidence. They are not a security sandbox. You cannot upgrade V3 captures to V4. Rerun the original scenario. Distinguish root exit from descendant settlement. Require freshness when the task needs it.

A timed-out target that must write a final report needs `finalization_ms`. REA sends SIGTERM to the captured root and continues to observe. REA sends SIGKILL one time, only if the target has not exited by the end of the interval.

These fields record the result:

- `exit.finalization.signals` lists each attempt with its delivery result.
- The observed exit signal stays in `exit.signal`.
- `exit.code` is null for every deadline exit.
- The code of a finalizer that exited on its own is in `exit.finalization.exit_code`. It is null on a signal exit or when REA observed no exit.

Use `inspect_analysis_view` with `view: {kind: "api-surface"}` on retained `inspect_web_network_capture` HAR Evidence. It groups observed request lines by method, origin and path template.

Format matches are inferences. They do not show handler identity or schemas. Group pages cite parent record ordinals. Read the record coverage and exclusions before you claim completeness. REA does not replay requests.
