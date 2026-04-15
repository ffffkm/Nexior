# Nexior Vercel Setup Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Make the upstream Nexior project run locally on Windows and leave behind the minimum repo changes needed to reproduce the setup and hand off a Vercel deployment cleanly.

**Architecture:** Keep Nexior in its default hosted-backend mode. Pin the local toolchain, document the Windows-specific install path, then verify build, dev startup, and auth entry flows without replacing any upstream backend services.

**Tech Stack:** Vue 3, Vite 7, TypeScript, Node 20.x, Yarn Classic, PowerShell, Vercel

---

### Task 1: Pin the local toolchain

**Files:**
- Create: `.nvmrc`
- Modify: `package.json`

- [ ] **Step 1: Add the Node version file**

```text
20.20.2
```

- [ ] **Step 2: Pin Yarn Classic in `package.json`**

```json
{
  "packageManager": "yarn@1.22.22"
}
```

- [ ] **Step 3: Verify the chosen runtime versions**

Run:

```powershell
node -v
yarn -v
```

Expected:

```text
v20.20.2
1.22.22
```

### Task 2: Write the operator handoff doc

**Files:**
- Create: `docs/deploy/LOCAL_VERCEL_SETUP_ZH.md`
- Reference: `src/constants/endpoint.ts`
- Reference: `vercel.json`

- [ ] **Step 1: Document the supported local install command for Windows**

Snippet to include:

```text
On Windows, prefer `yarn install --ignore-scripts`.
```

- [ ] **Step 2: Document the local verification commands**

Snippet to include:

```powershell
yarn build
yarn dev
```

- [ ] **Step 3: Document the Vercel deployment path**

Snippet to include:

```text
1. Fork to a personal GitHub repository
2. Import that repository in Vercel
3. Keep the default build settings
4. If prompted, use Node 20.x and Yarn
```

### Task 3: Verify runtime behavior

**Files:**
- Verify only: local workspace, generated `dist/`

- [ ] **Step 1: Install dependencies with the Windows-safe command**

Run:

```powershell
yarn install --ignore-scripts
```

Expected: install completes without the `@stellar/stellar-sdk` postinstall failure.

- [ ] **Step 2: Verify the production build**

Run:

```powershell
yarn build
```

Expected: Vite build succeeds and outputs `dist/`.

- [ ] **Step 3: Verify local startup**

Run:

```powershell
yarn dev
```

Expected:

```text
Local:   http://localhost:8084/
```

- [ ] **Step 4: Verify unauthenticated auth flow**

Check in a browser:

```text
http://127.0.0.1:8084
```

Expected:

```text
The app redirects to AceDataCloud auth login and preserves the local redirect URL.
```

- [ ] **Step 5: Verify the register entry**

Check in the auth page:

```text
还没有账户？注册
```

Expected:

```text
Navigation reaches /auth/register and shows email registration fields.
```
