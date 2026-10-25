# The FrameCore bridge

Everything the panel asks After Effects to do goes through one function, one
protocol, and one place to look when it breaks.

```
panel UI            client/js/app.js            (no AE knowledge)
   |
connection state    client/js/host-status.js    (is the host answering?)
   |
bridge              client/js/bridge.js         (the only evalScript caller)
   |
ExtendScript        host/FrameCoreHost.jsx      (all AE logic, ES3)
```

`CSXS/manifest.xml` loads the host script with `<ScriptPath>`, so
`FrameCoreHost.dispatch` exists before the panel's first call. The panel loads
`js/lib/CSInterface.js` (Adobe v12.0.0, unmodified), then `bridge.js`, then
`host-status.js`, then `app.js`.

## Protocol

One string in, one string out. No field is ever concatenated into script text:
the payload is serialised, then that string is serialised again so it can be a
JavaScript string literal inside `evalScript`.

```js
FrameCoreHost.dispatch('{"method":"ping","params":{}}')
```

Success:

```json
{ "ok": true, "data": { "version": "0.1.0", "protocol": 1, "host": "After Effects",
                        "aeVersion": "26.0", "hasProject": true } }
```

Failure:

```json
{ "ok": false, "error": { "code": "E_NO_COMP",
                          "message": "No composition is selected. Open a composition in the timeline, then try again.",
                          "detail": null } }
```

`detail` is for diagnostics only (a raw message, a line number, the offending
method name). UI must show `message`, never `detail`, and never a stack trace.

A method must return plain values only: strings, numbers, booleans, `null`,
arrays and objects built in the script. Never return an After Effects object
(a `CompItem`, a `File`, a property) — the JSON writer enumerates own properties,
so a host object would serialise as `{}` or as a stale snapshot. Convert first,
and let `getContext` be the model for that.

From the panel:

```js
FrameCoreBridge.call("getContext").then(function (context) {
  // context.projectOpen, context.comp.width, ...
}, function (error) {
  // error.code, error.message — already plain language
});
```

`call` rejects with an `Error` carrying `.code`, `.message`, `.detail` and
`.framecore === true`. There is no other way to reach the host.

## Error codes

Host side (`host/FrameCoreHost.jsx`), chosen where the cause is known:

| Code | When | What the user is told |
|---|---|---|
| `E_PROTOCOL` | payload is not readable JSON, or has no method | reload the panel |
| `E_UNKNOWN_METHOD` | panel and host are different builds | reinstall |
| `E_NO_PROJECT` | no project open | open or create a project |
| `E_NO_COMP` | active item is not a composition | open a comp in the timeline |
| `E_COMP_LOCKED` | comp padlock is on | unlock it in the Project panel |
| `E_UNDO` | `app.beginUndoGroup` refused | open a project first |
| `E_HOST` | anything uncaught, or a raw AE error | retry, then report the exact steps |
| `E_SERIALIZE` | the reply could not be serialised | retry |

Panel side (`client/js/bridge.js`), chosen when the host never answered:

| Code | When | What the user is told |
|---|---|---|
| `E_NO_HOST` | panel opened outside AE (`__adobe_cep__` absent) | open it inside After Effects |
| `E_SCRIPT` | `evalScript` returned `EvalScript error.` | reinstall, reload AE |
| `E_EMPTY` | empty reply | reload the panel |
| `E_PROTOCOL` | reply is not our JSON envelope | reinstall, reload AE |
| `E_EVAL` | the call itself threw | reload the panel |
| `E_TIMEOUT` | no answer within the budget (20 s default) | wait, then reopen the panel |
| `E_WEDGED` | a previous call timed out and the host is still busy | wait for it to finish |
| `E_BUSY` | 32 calls already queued | let the current task finish |

Timeouts are a watchdog, not a cancel: `evalScript` cannot be aborted, so a late
answer is discarded and the host is marked unwedged when it eventually arrives.

## Serialisation, not parallelism

`evalScript` is asynchronous in the panel but the script runs on After Effects'
main thread, which also dispatches CEP events (Adobe CEP 12 cookbook, line 1184).
Two host calls never run at the same time, and interleaving them would break undo
groups, so `bridge.js` keeps exactly one call in flight and queues the rest.

Anything that blocks the host for more than **750 ms** is logged to the panel
console with its method name and duration. That warning is the signal to split
the host method into smaller calls. Phase 6 builders are expected to chunk layer
creation rather than build a whole animation in one `evalScript`.

## Undo

Every change goes through `FrameCoreHost.transaction(label, work, rollback)`:

1. `app.beginUndoGroup(label)`
2. `work()`
3. on throw, `rollback()` runs **before** the group closes
4. `app.endUndoGroup()`
5. the original error is rethrown

So a failed generation leaves either nothing (rollback succeeded) or one undo
group that the user can remove with a single Ctrl+Z. A rollback that itself
throws is swallowed so the original problem is the one reported.

Phase 3 has no method that changes the project, so the undo path is proven only
by `selfTest` (group opens, failure rolls back, group closes). The first real
test of Ctrl+Z comes with the Phase 6 layer builders.

## Testing

Automated, no After Effects needed:

```powershell
python tools\check-hostjs.py      # ES3 bans, ASCII-only, Node parse of the host
node tests\host-json.test.js      # 26 tests: JSON polyfill vs native JSON, dispatch, undo
node tests\bridge.test.js         # 16 tests: transport, reply validation, queue, timeouts
```

`tests/bridge.test.js` runs `client/js/bridge.js` in a vm with a fake
`window.__adobe_cep__`, so the panel half is tested too: double-encoded payloads,
`EvalScript error.`, empty replies, host error envelopes, serialisation of
concurrent calls, `E_TIMEOUT` latching `E_WEDGED`, the 32-call queue cap and the
`E_NO_HOST` path.

`check-hostjs.py` bans `const`/`let`, arrows, template strings, spread, classes,
optional chaining, `Promise`, `JSON.*`, `Object.keys`, `Array.isArray`,
`forEach`/`map`/`filter`/`indexOf`/`trim`, trailing commas, non-ASCII characters
and `window`/`document`. It strips comments and strings first, so prose about
ES3 does not trip it.

Manual, inside After Effects (`docs/INSTALL.md` to load the panel):

1. Open the panel. DevTools console should show
   `connected to After Effects <version> (host 0.1.0)`.
2. Or open `client/dev/bridge-test.html` in the panel. Easiest way: with the
   panel focused, DevTools console:
   `location.href = "dev/bridge-test.html"`. Reload the panel (`Ctrl+R`) to get
   back to the UI. (Pointing `<MainPath>` at it also works, but then the panel
   opens on the test page.)
3. Press **Ping**: `aeVersion`, `hasProject`, `protocol: 1`.
4. Press **Context** with no project open: `ok: true`, `projectOpen: false`.
   Open a project and a comp, press again: width, height, frame rate, duration,
   layer count.
5. Press **Self test**: `undoGroupRuns`, `rollbackRuns`, `jsonRoundTrip` all
   true, `undoError: null`.
6. Press **Unknown method**: `E_UNKNOWN_METHOD` and a readable sentence.
7. Lock a comp (padlock in the Project panel) and press **Context**. The status
   stays a status call, so this does not error. Phase 6 build calls will use
   `comp()`, which returns `E_COMP_LOCKED`.

Open the test page in Chrome too: it reports `absent — this page is in a browser`
and keeps working instead of throwing. That is the `E_NO_HOST` path.

## Not in this phase

- No provider, network, schema, routing or generation code.
- No file writing, so After Effects' *Allow Scripts to Write Files and Access
  Network* preference is not needed yet; the render phase will document it.
- The status card in the panel is still static copy. Phase 6 binds it to real
  state.
- AE 2024 and 2025 are untested on this machine (only 2026 is installed). The
  host script uses no version-specific API.
