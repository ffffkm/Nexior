# Nexior Vercel Setup Design

## Goal

Run the upstream Nexior project locally on Windows and prepare it for a zero-server Vercel deployment that keeps the default AceDataCloud backend, authentication, payment, and referral behavior.

## Scope

This setup keeps Nexior in its default mode:

- Frontend is built and served by Vercel.
- Authentication, AI abilities, and payment/referral logic remain on AceDataCloud services.
- No custom API backend, no custom payment merchant account, and no white-label redesign are added in this phase.

## Architecture

The deployed system has three parts:

1. Source code lives in the user's own GitHub fork of `AceDataCloud/Nexior`.
2. The frontend is built from that repository with `Node 20.x` and `yarn` and then hosted on Vercel.
3. Runtime requests continue to use AceDataCloud defaults:
   - `https://auth.acedata.cloud`
   - `https://platform.acedata.cloud`
   - `https://hub.acedata.cloud`

The source code already contains these default endpoints in [`src/constants/endpoint.ts`](E:/deim/aiweb/nexior-src/src/constants/endpoint.ts), so local and Vercel builds can run without custom environment variables.

## Environment Requirements

- Node.js `20.x`
- Yarn Classic `1.22.x`
- Git for syncing to a personal fork
- Vercel account connected to GitHub

## Windows Constraint

On Windows, `yarn install` can fail because a transitive dependency (`@stellar/stellar-sdk`) runs a POSIX-style `postinstall` command (`yarn setup || true`). That script is not required for Nexior runtime, so the supported Windows installation path for this workspace is:

```powershell
yarn install --ignore-scripts
```

## Verification Targets

The setup is considered successful when all of the following are true:

1. Dependencies install successfully with Node `20.x` and Yarn Classic.
2. `yarn build` completes successfully.
3. `yarn dev` serves the app locally on port `8084`.
4. Visiting the local app redirects unauthenticated users to AceDataCloud auth as expected.
5. The AceDataCloud login page exposes working login and register entry points.
6. The repository is ready to be imported into Vercel with default settings.

## Known Limits

- A full authenticated chat session cannot be completed automatically here without a real AceDataCloud account or OAuth login under the user's control.
- Vercel deployment itself still requires the user to complete GitHub/Vercel authorization in the browser.
