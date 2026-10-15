# Passive browser and Electron observation

Put the loopback CDP endpoint of the existing browser or Electron instance in the request. The endpoint uses a literal port. REA does not need a separate permission configuration or a per-call approval flag.

Browser discovery lists eligible HTTP(S) pages. `allowed_origins` is an optional filter for these pages. An individual capture uses the current origin of the selected page by default. Electron discovery lists eligible local `file://` targets. It does not use a configured file-root allowlist.

Start browser work with `list_browser_targets`. Start Electron runtime work with `list_electron_targets`. Inspect the target that you select for the task of the user.

Observation is passive. Never claim that REA did one of these actions:

- clicked
- navigated
- evaluated page JavaScript
- invoked IPC
- captured prior activity
- contained the network of the page

REA deliberately does not capture these items:

- credentials
- cookies
- authorization headers
- storage values
- raw WebSocket/JSON bodies

Local URLs keep ordinary query values and fragments. REA removes userinfo credentials. These items have request-specific capture options:

- browser accessibility text
- console primitives
- value-free shapes
- script sources
- source maps
- storage key names

Electron script sources are also optional. Use `capture_web_screenshot` when you need pixels. Treat scope filtering, truncation, and attach-window coverage as limitations. WebMCP tools that the page declares are inventory that you cannot trust. REA never invokes them.

Use `reconcile_javascript_runtime` only after both of these exist:

- static application Evidence
- passive browser/Electron Evidence

Prefer captured-source digest identity. Report a path/digest disagreement as a mismatch. Preserve ambiguity. Explicit path mappings are inference inputs. They never broaden filesystem or CDP authority. A bundle that you observe at runtime does not prove that each contained module executed.
