# Native, managed, and packaged artifacts

## Go build metadata

Use `inspect_go_binary` for an explicit local ELF, PE, or thin Mach-O. Use it when the question is about one of these items:

- the embedded compiler version
- the module and dependency versions
- local replacements
- build settings

The tool does not need `open_binary`, a Go installation, or target execution. The CLI equivalent is `inspect-go-binary PATH`.

Keep these items:

- the artifact SHA-256
- the original file ranges
- the framed module bytes
- every unparsed module-text line

A missing record is an unknown. It is not proof of a non-Go binary. Observed metadata does not authenticate its claims. Function, source, and type recovery are a separate native-analysis question.

## SQLite snapshots

Use `inspect_sqlite_database` with an explicit local database snapshot. It gives the schema and, as an option, rows from one exact ordinary table. It needs Node.js 24.x >=24.15 or 26+ with the native `DatabaseSync.limits` API. It also reads the existing sibling `-wal`. Keep both original digests when you cite its result.

The default output is a compact inventory. A selected table expands only its own schema and samples 20 rows. Select `columns` to avoid irrelevant or large cells. Request `schema_scope: "database"` only for complete schema details.

Choose `row_limit` when a finite sample is useful. Keep truncation and unspecified row order explicit. Virtual tables, shadow tables, and views are schema facts. They are not row-read targets.

REA inspects private copies and leaves the source files untouched. Before inspection, obtain a coherent quiescent database/WAL snapshot. See [SQLite snapshots](https://github.com/morluto/rea/blob/main/docs/sqlite-databases.md).

## Recorded Linux crashes

Use `inspect_recorded_crash` with an explicit Linux x86-64 ELF core path. It inspects these historical items:

- thread registers
- signals
- raw notes
- original file ranges

It does not depend on the active disassembler target. Source-bound register values and note bytes are observations. These items remain unknown:

- missing notes
- signal-thread association
- current executable and library identity

PIDs are historical metadata. They never authorize live attach or control.

The optional `include_debugger_context` facet adds mapping candidates. It uses the GDB/pwndbg that the caller supplies, in an owned core-only session. Display names do not establish current file identity. Zero reported flags leave permissions unknown. If the requested context is unavailable, the tool fails with setup guidance. See [recorded crashes](https://github.com/morluto/rea/blob/main/docs/recorded-crashes.md) for exact upstream profiles, bounds, and verification coverage.

## Offline ELF layout

Use `inspect_binary_layout` with an explicit local ELF path. It gives these items without an open disassembler target:

- file-backed sections
- symbols
- relocations
- linkage names
- static mitigation inferences

When the complete record is already retained, call `inspect_analysis_view`. It gives one of these items:

- a summary
- a mitigations facet or a linkage facet
- one section or one symbol
- a stable page

Do not run the decoder again to read a single object.

## Offline WASM

Use `inspect_wasm_artifact` with the WABT that the caller supplies, for selected local bytes. For counts, validation, and WAT identity, select `detail: "summary"` on the first MCP call. The complete original Evidence stays retained. Use `normalized_result.parent_evidence_id` for exact exports or text pages. Omitted or complete detail keeps the existing complete result.

For a completed result, use `inspect_analysis_view` with its reported retained reference. This gives a summary or `view: {kind: "wasm", facet: "exports", offset: 0, limit: 32}`. The facets also include these items:

- sections
- imports
- candidates
- glue
- WAT
- headers
- details
- tool profile

Text offsets count UTF-16 code units. Follow `next_offset`. The complete source remains retained after an oversized MCP reply. The tool does not imply target execution or network acquisition. WAT is a decoded representation.

## Native targets

After `open_binary`, use the focused search, procedure, or function tools directly. Use `binary_overview` when target metadata or inventory context helps to answer the question. Prefer literal search, names, decompilation, callers, callees, and cross-references.

Addresses and recovered pseudocode are analysis observations. They are not original source. Provider unavailability and unsupported metadata leave the fact unknown. They do not make it false.

For a completed `analyze_function` dossier, use `inspect_analysis_view` with `view: {kind: "native", facet: "value_flow_operations", offset: 0, limit: 32}` and the exact retained Evidence ID. This reads selected facts without reanalysis. The native facets also include these items:

- procedure
- pseudocode
- assembly
- basic blocks
- comments
- callers/callees
- references
- unresolved calls
- referenced strings and names
- the native API record
- value-flow summaries

Pseudocode offsets and limits count UTF-16 code units. Use the returned `next_offset` to continue. Provider limitations and unavailable facts remain explicit. REA retains the complete parent Evidence.

To see which functions or Objective-C methods a Mach-O calls during one run, use `observe_native_calls`. Give it explicit breakpoints and a bounded `duration_ms`/`max_events`. It launches a new process under LLDB. The target runs and can change files or use the network. Hardened-runtime targets need the `get-task-allow` entitlement.

Use `binary_session` with no arguments to check the open target, the selected provider, and the alignment. Its default `result.tool_availability` carries a complete inventory grouped by shared facts. Expand the `names` of each group. Inherit the inventory-level client requirements, unless a group overrides them. Availability, reasons, remediation, modes, and flags remain inline.

The `effects` and `annotations` arrays name true flags from fixed vocabularies. An omitted known flag is false. `tools/list` remains the canonical source for full tool schemas when the target or provider state changes.

Provider capability groups carry the provider identity and ordered limitations. To recover the full ordered list for an operation, prepend the `shared_limitations` of each set to the `limitations` of a group. Candidate capabilities describe possible providers. Top-level session capabilities describe the current route. `analysis_provider_binding` identifies the selected provider.

## DOS MZ with Ghidra

For an admitted DOS MZ target, use `inspect_native_load_image`. It checks the measured loaded bytes, file mappings, relocations, and the entry against the immutable snapshot. Keep `mismatch` and `unsupported` explicit. Verified import covers the reported static image. It does not cover runtime DOS or PC-98 hardware behavior.

Use `read_bytes` for initialized analysis memory. Use `address_to_file_offset` to anchor one observed address to original file bytes. Loader fixups can change a word. A source offset does not imply byte equality.

Complete function body ranges use inclusive ends and can contain gaps. Do not treat the enclosing span as code. For packed targets, retain the original artifact identity and the separately derived artifact identities. Decompiling an unpacking stub does not recover the unpacked program.

When you read Objective-C dispatch metadata, distinguish class implementation methods from protocol requirements:

- Class methods report null requirement flags.
- Decoded protocol declarations retain required and optional facts.

Property attributes remain raw name/value tokens. Use the `atomicity` field of the property for the decoded `N` marker or the default atomic behavior. A null value means that the selected provider did not establish the fact. This applies to these values:

- class/meta flags
- ivar counts
- atomicity
- read-only values

Symbol names alone do not prove that a class is a root class or has no ivars.

## Managed PE/CLI

Start with `inspect_managed_artifact`. The canonical managed inspection of REA is execution-free. Do not claim that it loaded, reflected, executed, or resolved the assembly.

Keep the managed/native boundaries explicit. Keep unavailable reconstruction facts explicit. A bring-your-own reconstruction oracle is separate from the canonical parser. It must not become an implicit setup dependency.

### .NET NativeAOT

NativeAOT binaries contain native machine code. Ordinary CIL decompilers cannot recover the original method bodies. When managed metadata/CIL is absent, classify the artifact as a native-analysis route. Do not claim source recovery.

Open the native image with the selected deep provider. Inspect functions, strings, calls, and references. Describe decompiler output as pseudocode and inference.

NativeAOT images can be PE/COFF, ELF, or Mach-O executables and shared libraries. Use file bytes and loader metadata, not suffixes, to identify them. For packaged apps, inventory and extract with the artifact tools of REA. Retain the exact contained image identity. PDB, ELF debug, or dSYM sidecars are additional evidence only when the provider accepts and matches them.

REA has a built-in read-only snapshot parser for x64 PE targets with .NET 8 RTR 9.1 metadata. An optional headless Ghidra adapter adds database annotations and broader metadata recovery on Linux. It needs an explicitly configured NativeAOT JAR. These are separate routes.

See the [NativeAOT guide](https://github.com/morluto/rea/blob/main/docs/ghidra-nativeaot.md) for setup, supported layouts, and host restrictions. Preserve the binary identity. Distinguish derived metadata from Ghidra database observations. Recovered types and native pseudocode are not original C# source. Windows Ghidra P0 has no database mutation authority.

## Packages and extraction

Use `inspect_artifact` for these inputs when the artifact graph and findings help to answer the question:

- application bundles
- archives
- ZIP/APK/IPA/MSIX/AppX
- ASAR
- DMG

It returns the complete artifact graph inline. When you use graph manifest IDs, cite them.

For an IPA or a macOS `.app`, ZIP, or DMG, pass the inventory Evidence to `project_apple_application_graph`. It reports application roots and nested bundles by path convention. The nested bundles are:

- app extensions
- XPC services
- frameworks
- login items
- system and driver extensions
- plug-ins

It also lists these items:

- privileged helpers
- launchd plists
- symlinks
- the `info_plist_path` and executable candidates of each bundle

Roles are path conventions. They are not parsed plists. Read the listed plists with `inspect_plist`.

After `open_binary` on a `.app` or Mach-O, use `trace_dylib_resolution`. It shows which file each `@rpath`, `@loader_path`, and `@executable_path` load reaches for every executable in the bundle. It also lists missing or weak loads and earlier `@rpath` candidates that are absent. Narrow large bundles with `roots` or `architecture`. System paths stay undetermined, because the dyld shared cache provides them.

Use `extract_artifact` when you need materialized files. It takes no arguments. It materializes all regular files into a fresh temporary directory that REA chooses. Symlinks and encrypted entries are inventory facts. They are not extractable files. The result reports the materialized directory.

The caller does not choose the destination. You do not need a separate permission grant.

Native DMG traversal automatically uses a read-only mount on supported macOS hosts. It uses an owned temporary mount directory and cleans it up. It needs no approval flag. Unsupported hosts and mount failures remain explicit limitations. Do not claim child inventory when only the root identity is available. Extraction is a separate filesystem-writing operation.

For a headerless DOS COM, open it explicitly with `format: "dos-com"` (CLI `--target-format dos-com`). Inspect the load image before you trust function analysis. The entry is `0x10100`, with file offset zero and an imposed real-mode segment context. The PSP/stack/device state remains unmodeled. See [DOS guide](https://github.com/morluto/rea/blob/main/docs/ghidra-dos.md).

For other headerless images, open them with `format: "raw-binary"`, `architecture` (`x86`, `x86_64`, `arm`, or `arm64`), and `entry_point` (`0x` hex). An example is firmware for an embedded device, without ELF/Mach-O/PE/MZ headers. REA maps file offset 0 at that address.

Hopper and IDA refuse this interpretation. Ghidra uses `BinaryLoader` with a little-endian generic language. The `raw-binary` route does not support big-endian, Thumb-only, or Cortex-M variants. See [raw binary guide](https://github.com/morluto/rea/blob/main/docs/raw-binary.md).

### Function annotations in Ghidra

Use `annotate_native_function` for one function name and/or entry comments. You must give at least one explicit change. Review its annotation readback and refreshed dossier inline.

- Changes are atomic and session-scoped.
- Empty comments clear the comments. Omitted fields preserve them.
- Later MCP calls observe edits until close.
- CLI `annotate-native-function` returns the updated analysis before it discards the session.
- Original executable bytes stay the same. Edits invalidate immutable snapshots.
- Windows P0 does not admit database mutations.

For naming only, `set_address_name` / `set_addresses_names` also work with Ghidra. They name function entries or data addresses (primary labels). They return compact per-address success booleans, not a dossier.

## Offline PE resource inventory

Use `inspect_pe_resources` with an absolute `path`, or `rea inspect-pe-resources ./selected.exe --json`. It gives portable PE32/PE32+ resource inspection without an open native target. Preserve the returned Evidence:

- type/name/language IDs and UTF-16 names
- original directory/data/payload ranges
- code pages
- payload hashes
- static icon-group references

Same-language icon resolution is explicit. Missing references and size mismatch remain visible. Other payloads are opaque. This operation does not decode images or simulate Windows resource loading. For the budgets and the supported tree profile, see [offline binary diagnostics](https://github.com/morluto/rea/blob/main/docs/binary-diagnostics.md).
