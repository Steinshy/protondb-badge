# Architecture — ProtonDB Status

## Overview

ProtonDB Status is a Millennium plugin: a TypeScript frontend injected into every Steam client window, plus a Lua backend that makes the network calls (Steam webviews cannot reach ProtonDB directly because of CORS). `millennium-ttc` bundles `frontend/` into `.millennium/Dist/index.js`, which is what Steam loads.

```text
Steam window ── index.tsx (window hook) ── observer.ts (per-window loop)
                                              ├─ detector.ts ── appId + title
                                              ├─ badge.ts ───── stats row lookup + badge DOM
                                              └─ protondbApi.ts ── callable('FetchProtonDb')
                                                                     └─ backend/main.lua ─┬─ Steam store (appdetails, storesearch)
                                                                                          └─ ProtonDB summaries API
```

## Modules

| Module                             | Responsibility                                                                   |
| ---------------------------------- | -------------------------------------------------------------------------------- |
| `plugin.json`                      | Millennium manifest (name, version, `backendType: lua`)                          |
| `frontend/index.tsx`               | Entrypoint: `AddWindowCreateHook` for `SP …` windows, settings panel             |
| `frontend/injection/observer.ts`   | Per-window state: MutationObserver + 500 ms interval, badge placement/removal    |
| `frontend/injection/detector.ts`   | Current game detection: Desktop pathname, Big Picture route-patch data           |
| `frontend/display/badge.ts`        | Stats-row lookup (incl. popups), badge DOM, `TIER_COLORS`, ProtonDB link         |
| `frontend/services/protondbApi.ts` | Backend callable wrapper, session rating cache, non-Steam appId detection        |
| `backend/main.lua`                 | `FetchProtonDb`: title → appId resolution, native Linux check, ProtonDB fetch    |
| `.github/workflows/release.yml`    | `v*` tag → `npm ci`, build, zip `protondb-status/`, GitHub release               |

## Invariants

- **Per window:** one `WindowState` per Steam window name (not per `Document`); a known name with a new document tears down the stale observer and re-attaches.
- **Single pass:** MutationObserver and interval both go through one 100 ms debounce; `processingAppId` blocks overlapping runs for the same game.
- **Stale results:** `currentAppId` is re-checked after every `await`; a late response for a previous game is dropped.
- **Stable badge:** an existing badge is only moved back to the end of the row, never re-fetched or rebuilt while the same game is shown.
- **Placement:** waits up to 400 ms for the Achievements stat so the badge lands after it.
- **Detection:** Desktop reads `/app/<appid>` from `MainWindowBrowserManager.m_lastLocation`; Big Picture patches `/library/app/:appid` via `__ROUTER_HOOK_INSTANCE`, trusted only for Big Picture windows.
- **Classnames:** Steam CSS classes are resolved at runtime with `findClassModule`, never hardcoded (hashes change between client updates).
- **DOM scope:** lookups use the window's own `Document` and its `g_PopupManager` popups, never the global `window`.
- **Non-Steam games:** appIds `>= 0x80000000` send `appId: 0` + title; the backend accepts only an exact case-insensitive store-search match.
- **Native tier:** backend checks store `appdetails` `platforms.linux` first and returns `native` without calling ProtonDB; definitive answers cached, failures fall through to ProtonDB.
- **Errors:** backend returns `{ error = "…" }` JSON instead of throwing; any error or missing `tier` means no badge.
- **Caching:** frontend caches ratings (including `null`) for the whole Steam session.

## Testing

- No automated tests or linter; `npm run build` succeeding is the only check.
- Manual: build, install into `~/.local/share/millennium/plugins/protondb-status`, restart Steam, and check Desktop + Big Picture game pages (rated, Pending, native, non-Steam shortcut).

## See also

- `CLAUDE.md`, `README.md`
- Millennium docs: https://docs.steambrew.app
