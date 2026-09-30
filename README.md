# ProtonDB Status

Millennium plugin that shows ProtonDB compatibility directly in the game details stats row (next to Play Time / Achievements) in Steam, in both Desktop and Big Picture mode.

![ProtonDB Status Screenshot](images/screenshot.png)

---

## Features

- Shows the ProtonDB tier, or Native for games with a native Linux build
- Works for non-Steam shortcuts (matched by exact store title)
- Badge appears only when a rating exists
- Click the badge to open the game's ProtonDB page

| Tier     | Color     |
| -------- | --------- |
| Native   | `#008000` |
| Platinum | `#b4c7dc` |
| Gold     | `#cfb53b` |
| Silver   | `#a6a6a6` |
| Bronze   | `#cd7f32` |
| Borked   | `#ff0000` |
| Pending  | `#a6a6a6` |

---

## Requirements

- Millennium installed
- Steam desktop client

---

## Install

### Marketplace

Working on getting it added.

### Releases

1. Download the latest release.
2. Extract it into your Millennium plugins directory so this path exists:

| OS      | Path                                              |
| ------- | ------------------------------------------------- |
| Linux   | `~/.local/share/millennium/plugins/protondb-status` |
| Windows | `%MILLENNIUM_PATH%\plugins\protondb-status`       |

3. Restart Steam, then enable the plugin from Millennium plugins settings.

If you end up with a nested folder like `protondb-status/protondb-status`, move the inner folder up one level.

---

## Repository

```text
plugin.json        Millennium manifest
backend/main.lua   Lua backend (ProtonDB + Steam store requests)
frontend/          TypeScript frontend (injection, badge, API bridge)
images/            README assets
```

See `ARCHITECTURE.md` for how the pieces fit together.

---

## Development

```bash
npm ci
npm run build   # → .millennium/Dist/index.js
```

Copy or symlink the repo to your Millennium plugins directory and restart Steam to test. Releases are built by GitHub Actions when a `v*` tag is pushed.

---

> [!NOTE]
> **AI Disclaimer**: Parts of this project were assisted or written by AI. If that's something you're not comfortable with, no hard feelings, I understand and I don't force anyone to use it. The code may have flaws. If you spot something that could be better, contributions are very welcome. I'm still learning and would appreciate the help.
