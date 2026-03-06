# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```sh
# Install dependencies
yarn

# Start Next.js web dev server
yarn web

# Start Expo native dev server (requires a dev client already built on device/simulator)
yarn native

# Build iOS dev client (from apps/expo)
cd apps/expo && expo run:ios

# Build Android dev client (from apps/expo)
cd apps/expo && expo run:android

# Build Next.js for production
cd apps/next && yarn build

# Lint Next.js app
cd apps/next && yarn lint
```

## Architecture

This is a **Solito monorepo** — a cross-platform app sharing code between Expo (React Native) and Next.js (web).

### Monorepo layout

- `apps/expo` — Expo/React Native app entry point. Wraps `packages/app` with `<Provider>` and `<NativeNavigation>`.
- `apps/next` — Next.js 16 App Router web app. Pages re-export screens from `packages/app`.
- `packages/app` — All shared UI and business logic. Import this from both apps.

### Shared code in `packages/app`

- `features/` — Organize by feature (not by `screens/`). Each feature exports a screen component used by both platforms.
- `provider/` — App-level providers (SafeArea, Navigation). Platform-specific implementations use `.native.tsx` suffix.
- `navigation/native/` — React Navigation stack for Expo only. Web navigation is handled by Next.js file routing.

### Platform-specific files

Files with `.native.ts(x)` suffix are used on Expo; the plain `.ts(x)` version is used on web. Next.js resolves `.web.tsx` extensions first via webpack/Turbopack aliases.

### Navigation pattern

- **Web**: Next.js App Router handles routing. Each `apps/next/app/` page re-exports the corresponding feature screen from `packages/app`.
- **Native**: React Navigation native stack defined in `packages/app/navigation/native/index.tsx`.
- **Cross-platform links**: Use `solito/link` (`TextLink`) for links that work on both platforms.

### Dependency rules

- Pure JS dependencies shared across platforms → install in `packages/app`
- Native libraries (with native code) → must install in `apps/expo`; optionally also in `packages/app` at the **exact same version**

### Key packages

- `solito` — Cross-platform navigation primitives
- `moti` — Animations (wraps `react-native-reanimated`)
- `dripsy` — Cross-platform styling/theming
- React Compiler is enabled (Babel plugin in `apps/expo`)

### TypeScript

Root `tsconfig.json` maps `app/*` → `./packages/app/*`. `strictNullChecks` and `noUncheckedIndexedAccess` are enabled.

### Prettier

No semicolons, single quotes, 2-space indent (see root `package.json`).
