# ProtonDB Status

Millennium plugin showing a ProtonDB tier badge in the Steam game details stats row (Desktop + Big Picture). Fork of `nyakuoff/protondb-badge`.

## Guidance

- Architecture, module map, and invariants: `ARCHITECTURE.md`; read it before changing `frontend/injection/` or `backend/main.lua`.
- Keep this file to always-needed guidance.

## Repository

- `origin` = `Steinshy/protondb-badge` (fork); upstream = `nyakuoff/protondb-badge`.
- Work on `dev`; upstream-bound changes go on a fresh branch from `main`, one topic per branch.
- Never push to a branch backing an open upstream PR (`fix/pending-badge-blink` → PR #3) unless the change is meant for that PR.

## Development

- npm only (`package-lock.json` committed); don't add pnpm/yarn lockfiles.
- `npm ci && npm run build` → `.millennium/Dist/index.js` (gitignored); no tests or linter, a clean build is the only check.
- Test in Steam: copy/symlink to `~/.local/share/millennium/plugins/protondb-status`, build, restart Steam (backend changes always need a restart).
- Log with a `[ProtonDB]` prefix.
- Never hardcode Steam CSS classnames; resolve via `findClassModule` (`frontend/display/badge.ts`).
- Network calls go through the Lua backend only; return errors as `{ error = "..." }` JSON.
- Tier colors live in `TIER_COLORS` (`frontend/display/badge.ts`), lowercase keys.

## Known issues

- `package.json` version (1.0.0) ≠ `plugin.json` (1.3.0); `release.yml` names releases from `package.json`. Bump both when releasing.
- Settings toggle `protondb-status.show` is saved to `localStorage` but never read.
- Frontend cache keeps `null` results for the whole session; a failed lookup shows no badge until Steam restarts.
