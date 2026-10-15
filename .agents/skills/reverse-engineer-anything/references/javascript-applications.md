# JavaScript and Electron application artifacts

To recover readable source from captured or bundled JavaScript, use
`recover_javascript_sources` with an absolute `path` and no `output_directory`.
When the connected schema advertises `additional_paths`, supply related runtime, shared, and lazy files in it.
Then analyze the returned `analysis_input` directory together.

Each input keeps its own snapshot, digest, and byte-range provenance.
REA never executes inputs or recovered code.

Recovery needs a Wakaru that the caller supplies, on Linux x64.
See [the recovery guide](https://github.com/morluto/rea/blob/main/docs/javascript-recovery.md).

Use `analyze_javascript_application` directly on the ASAR or extracted tree that the operator supplies.
Select exact inventoried source paths only when both of these conditions are true:

- The question concerns known source paths.
- The connected schema advertises `source_paths`.

Then select exact inventoried paths, such as
`["package.json", "main.js", "preload.js"]`.

Include supporting manifests or source maps explicitly.
REA does not analyze dependencies implicitly.
REA retains the complete artifact inventory and identity.
REA reads source text and analyzes only the selected files.
The result and the summary record `source_selection`.

Files outside the scope, and their relationships, stay unknown.
Partial application coverage does not mean that the selected source failed.
Use repeated `--source-path` on either static application CLI route.
Omit the selection when the question needs complete application analysis.
`detail: "summary"` reduces delivery.
It does not by itself reduce analysis work.

Exact `#alias` specifiers resolve through the `imports` map of the enclosing package of the importer.
They resolve to package-local files.
Nested `node_modules` packages use their own map.
Wildcard, recursive, and external-package import targets stay unresolved with that limitation.

When the size is unknown or large, and the connected schema advertises `detail`, request `detail: "summary"`.
This returns an `inspect_analysis_view` Evidence record.
The record has a distinct ID.
It has a `normalized_result.parent_evidence_id` for the complete retained application analysis.
Source-map contents are part of that static analysis.
Read the findings, coverage, limitations, and unknowns.

Then follow a specific unanswered question.
Older servers without `detail` return the complete application Evidence inline when it fits.

When the connected schema advertises `max_heap_mb` and `analysis_timeout_ms`, these select two limits:

- `max_heap_mb` selects the old-space heap of the isolated source analyzer.
- `analysis_timeout_ms` selects the deadline for each source.

The defaults are 1024 MiB and 300000 ms.
They do not change the MCP process heap.
A Bun-hosted REA does not enforce the heap choice.
On Windows, it limits the committed memory of the analyzer.
The limit is the heap choice plus a 512 MiB runtime reserve.

Resource-limited analyses keep the completed static facts.
They report partial semantic coverage with specific unknown scopes.
This does not mean that the source is not valid.

Inspect those limits before you retry a source.
When expanded relationships exceed the available memory of the parent, inspect the modules item view of the file.
Look for the retained `static_analysis` and `semantic_module_analysis`.

Their facts stay available, even when graph links are unknown.
Cumulative result exhaustion can stop analysis before REA examines every file.
The inventory and the remaining unknown scopes show this case.

Timeout or cancellation after source analysis begins retains partial Evidence in the same session.
You can supply a returned `error.details.partial_observation` reference directly to `inspect_analysis_view`.
If the SDK cancels locally, retrieve its recorded ID from the Evidence bundle.
Do this before you inspect the partial analysis.

## Read a module without repeating analysis

Use the actual returned parent ID, on the same connection, as `source.evidence_id`.

The following IDs are placeholders.
Replace them with returned values.
When the node ID of the module is not known, page identities before you select an item.

Pages return only node IDs, paths, and roles.
Choose a useful page size.
Follow `coverage.next_offset` when another page matters:

```json
{
  "source": {
    "kind": "retained-evidence",
    "evidence_id": "ev_ACTUAL_PARENT_ID"
  },
  "view": { "kind": "page", "collection": "modules", "offset": 0, "limit": 16 }
}
```

Select the relevant page entry by its exact `node_id` and recorded role:

```json
{
  "source": {
    "kind": "retained-evidence",
    "evidence_id": "ev_ACTUAL_PARENT_ID"
  },
  "view": {
    "kind": "item",
    "collection": "modules",
    "selector": { "node_id": "jag_node_ACTUAL_NODE_ID" }
  }
}
```

Module pages include asset, bundled, and source module roles.

One path can identify several entries.

Use `selector.path` only when you establish that the path is unique.
The returned node ID removes the need for a complete inventory to prove uniqueness.

On ambiguity, select the matching node ID from the page or from the candidates in the error.
Follow `next_offset` when another page matters.
An item exposes these facts of that module node:

- the recorded observations
- the identity
- the hashes
- the exports
- the source ranges

An item does not include every child syntax observation, related edge, or original source file.
An absent item fact is not evidence of absence from the whole application.

## Trace only the remaining question

Use `trace_application_feature` on the parent application Evidence.
Use it for one literal node ID, route, string, API, IPC channel, module, or native export.
Select the direction:

- `incoming` for relationships that lead to the seed.
- `outgoing` for relationships that leave the seed.
- `both` when the question needs both.

Trace traversal includes containment relationships.
Even a narrow seed can expand many nodes.
A bidirectional trace can reach unrelated sibling modules.
When the observations of a module item or page already answer the question, prefer the item or page to a trace.
Do not treat all reached nodes as participants in the feature.

Reuse the retained parent Evidence when the server advertises it.
Otherwise, supply complete Evidence inline.
Include Hopper or Ghidra Evidence only when its artifact digest matches exactly.

## Recover an oversized display

An oversized response reports `resource_constraint` and
`details.resource: "transport"`.
Reuse `details.reported_limits.evidence_reference` with `inspect_analysis_view`.
Use it to get a summary, one module, or a stable page of module identities.
`export_evidence_bundle` writes the complete canonical session to a file that the caller selects.
These workflows keep the original Evidence and coverage.
A broad follow-up can also exceed framing.

A client can truncate a successful result inside its text envelope without a REA `resource_constraint`.

Treat a truncation notice or incomplete JSON as a display limitation.
Do not claim that you examined the full record.

Reuse the known parent ID for smaller views.
Alternatively, export complete Evidence to a selected file while the session stays open.

If the preview lost the parent ID, recover it from the prior analysis summary or from explicit ledger entries.
Do not invent an ID.
Do not rerun analysis only to reproduce the same display.

These items are static syntax observations:

- BrowserWindow preferences
- preload and contextBridge surfaces
- IPC registrations
- utility processes
- native binding requests

Only a unique exact literal IPC channel match supports an inferred pairing.
Dynamic or ambiguous channels stay unresolved.
A requested `.node` member is not a verified native export.
Never claim runtime reachability, registration, defaults, or policy enforcement from static analysis.

For version comparison, analyze each version once.
Then call `compare_application_versions`.
Accept only these matches:

- digest
- source-map
- structural fingerprint
- non-module semantic

Module ordinals and minified names are not persistent identity.
Report added or removed only with complete opposite-side coverage.
Otherwise, report unknown.

Use this workflow when the question asks how the returned object shape of one exact exported callable changed.

Analyze each version once.
Then call `compare_javascript_export_shapes` with explicit module paths and export names.
Use the returned IDs from both analysis calls on the same connection.
Alternatively, use the complete inline Evidence records from both calls.

Accept variant pairing only through the unique exact literal discriminant of the tool.
Before you treat `unknown` as "the name was not observed", read `property_inventories` and the `presence` of each change.
Inventories list observed property names even when values stay unknown.
Each paired or unpaired variant has a `source_range`.

Array holes and mutation-invalidated slots are not observed properties.
Complete parent coverage reports presence-only
`added`/`removed` independently of unresolved values.
Cite the comparison Evidence and report JSON Pointer changes.
These items stay unknown:

- dynamic values
- ambiguous variants
- incomplete parent-property coverage

This is static inference, not runtime behavior.
When you need runtime semantics, run behavioral probes against the relevant application versions.
Capture them through the available browser, Electron, or process workflows.

## Reusing application Evidence

When the connected server advertises it, application trace and compare tools accept two forms for their application input (`application`, or `left`/`right`):

- complete inline Evidence
- `{"kind":"retained-evidence","evidence_id":"ev_<64 lowercase hex characters>"}`

`inspect_analysis_view` uses the same retained-reference form in `source`, or portable inline Evidence.

It projects a summary, a module page, or one module with its recorded observations.
This notation is a template.
Replace it with the actual returned ID.

Versions before 4.1.0 accept only full inline Evidence.
Use the exact ID that the producer returned on the same MCP connection.
Resolution does not run analysis or select a provider.
Findings stay inline.
Native observation arrays still take full Evidence.

`close_binary` clears retained references.
Export a bundle before you close.
Alternatively, supply portable inline Evidence in another connection.
A missing reference includes its ID and recovery guidance.
