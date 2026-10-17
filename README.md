# NEXUS CLIENT

A premium, custom-branded Minecraft Java Edition launcher for Windows, built
with Electron, React, TypeScript and Vite.

> **Status:** This is a real, runnable application — not a mockup. It launches,
> manages installations and profiles, downloads from authoritative sources, and
> packages into a Windows installer. Some advanced features (Microsoft OAuth,
> full asset installation, in-game mod) are **partially implemented or
> unimplemented** and are clearly disclosed in
> [`docs/FEATURES.md`](docs/FEATURES.md). No functionality is faked.

---

## Quick start

```powershell
# 1. Install dependencies
npm install

# 2. (If Electron's binary did not download automatically) run its installer
node node_modules/electron/install.js

# 3. Run in development (Vite + Electron with HMR)
npm run dev

# 4. Type-check, lint and test
npm run typecheck
npm run lint
npm test

# 5. Build the renderer + main process
npm run build

# 6. Package a Windows installer (NSIS, x64)
npm run dist
```

The packaged installer is written to `dist_electron/`.

### Prerequisites

- **Node.js** 18+ (developed against Node 20/22; Node 26 also works)
- **Windows 10/11 x64**
- A **Java runtime** matching the Minecraft version you want to launch
  (Java 8 for ≤1.16, Java 17 for 1.17–1.20, Java 21 for 1.21+). The launcher
  auto-detects installed runtimes and can be pointed at a specific one.

---

## Folder structure

```
NexusClient/
├─ index.html                 # Renderer HTML entry (CSP lives here)
├─ vite.config.ts             # Vite + vite-plugin-electron build config
├─ tsconfig.json              # Renderer/shared TS config (strict)
├─ tsconfig.main.json         # Main-process TS config
├─ eslint.config.mjs          # ESLint flat config
├─ vitest.config.ts           # Test runner config
├─ scripts/
│  └─ generate-icon.cjs       # Dependency-free PNG/ICO brand icon generator
├─ build/                     # Generated icons + electron-builder resources
├─ src/
│  ├─ main/                   # Electron main process
│  │  ├─ index.ts             # App bootstrap, window, security policy
│  │  ├─ ipc.ts               # Validated IPC handlers (allowlisted)
│  │  ├─ store.ts             # Atomic JSON persistence (settings/profiles/installs)
│  │  ├─ logger.ts            # Structured logging with secret redaction
│  │  ├─ versions.ts          # Mojang version manifest client
│  │  ├─ java.ts              # Java runtime detection
│  │  ├─ downloads.ts         # Download queue (retry, cancel, dedupe)
│  │  ├─ launch.ts            # Game launch pipeline
│  │  └─ mods.ts              # Mod listing + Modrinth search
│  ├─ preload/
│  │  └─ index.ts             # contextBridge API (the only renderer surface)
│  ├─ shared/                 # Types, IPC channel names, branding
│  │  ├─ types.ts
│  │  ├─ ipc.ts
│  │  └─ branding.ts          # Single source of truth for product identity
│  ├─ core/                   # Pure, unit-tested logic (no Electron deps)
│  │  ├─ version-select.ts
│  │  ├─ memory.ts
│  │  ├─ download-state.ts
│  │  ├─ profile.ts
│  │  └─ ipc-validation.ts
│  └─ renderer/
│     └─ src/
│        ├─ App.tsx
│        ├─ components/       # Layout, Sidebar, TitleBar, icons, UI primitives
│        ├─ pages/            # Home, Versions, Mods, Skins, Accounts, Console, Settings, About
│        ├─ hooks/            # useSettings, useProfiles, useInstallations, useDownloads
│        └─ styles/           # Design tokens (CSS variables + styled-components theme)
├─ tests/
│  └─ unit/                   # Vitest unit tests
└─ docs/                      # Architecture, security, compatibility, features, release
```

---

## Environment configuration

No secrets are committed. Optional environment variables (all read in the main
process only):

| Variable | Purpose | Required |
| --- | --- | --- |
| `MICROSOFT_CLIENT_ID` | Azure application (client) ID for Microsoft OAuth | Only for Microsoft sign-in |
| `ELECTRON_RENDERER_URL` | Dev server URL (set automatically by `npm run dev`) | No |

Microsoft sign-in is **disabled** until a client ID is configured. The launcher
surfaces a clear message rather than faking a login. Offline profiles work
without any configuration.

---

## Documentation

- [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) — process model, data flow, adapters
- [`docs/SECURITY.md`](docs/SECURITY.md) — security model + dependency/license review
- [`docs/COMPATIBILITY.md`](docs/COMPATIBILITY.md) — feature/version compatibility matrix
- [`docs/FEATURES.md`](docs/FEATURES.md) — implemented / partial / unimplemented
- [`docs/BENCHMARKS.md`](docs/BENCHMARKS.md) — measured performance results
- [`docs/RELEASE.md`](docs/RELEASE.md) — installer build + release checklist

---

## License

MIT. Not affiliated with Mojang Studios or Microsoft. "Minecraft" is a
trademark of Mojang Synergies AB. This launcher does not bundle or redistribute
Mojang game files or third-party mod files.
