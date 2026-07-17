# Static Site Deployment Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [x]`) syntax for tracking.

**Goal:** Publish the static 山海经异兽图鉴 site to GitHub and Vercel from the correct project root.

**Architecture:** Treat `E:/NDM下载/山海经神兽全览/shanhaijing/` as the deployable project root. Keep the site framework-free and deploy it as static HTML with image assets.

**Tech Stack:** HTML, CSS, JavaScript, static assets, GitHub, Vercel.

---

## File Structure

- Create: `CLAUDE.md` — project-level rules, validation, and deployment conventions.
- Create: `.gitignore` — ignores local/editor/system artifacts without hiding source files.
- Modify: git metadata only — initialize repository in `shanhaijing/`, not the parent directory.
- External: GitHub repository — stores the static site source.
- External: Vercel project — serves production deployment.

### Task 1: Confirm Project Root and Rules

**Files:**
- Create: `CLAUDE.md`

- [x] **Step 1: Confirm deploy root**

Run:

```bash
ls "E:/NDM下载/山海经神兽全览/shanhaijing"
```

Expected: output includes `index.html` and `images/`.

- [x] **Step 2: Confirm parent is not the deploy root**

Run:

```bash
ls "E:/NDM下载/山海经神兽全览"
```

Expected: output includes both `images/` and `shanhaijing/`, so deploying the parent would include duplicate assets.

- [x] **Step 3: Write project rules**

Create `E:/NDM下载/山海经神兽全览/shanhaijing/CLAUDE.md` with static-site rules, validation commands, and deployment conventions.

- [x] **Step 4: Commit later with deployment prep**

Do not commit yet; commit after `.gitignore` and validation are done.

### Task 2: Validate Static Site Locally

**Files:**
- Inspect: `index.html`
- Inspect: `images/*.png`

- [x] **Step 1: Scan for obvious secrets**

Run:

```bash
rg -i "api[_-]?key|secret|token|password|passwd|BEGIN (RSA|OPENSSH|PRIVATE) KEY|AKIA[0-9A-Z]{16}|sk-[A-Za-z0-9_-]+|ghp_[A-Za-z0-9_]+|vercel_[A-Za-z0-9_]+" "E:/NDM下载/山海经神兽全览/shanhaijing"
```

Expected: no matches.

- [x] **Step 2: Check image references**

Run a script that extracts `images/*.png` references from `index.html` and verifies each file exists.

Expected: all referenced images exist.

- [x] **Step 3: Serve locally**

Run:

```bash
cd "E:/NDM下载/山海经神兽全览/shanhaijing"
python -m http.server 4173
```

Expected: server starts on port `4173`.

- [x] **Step 4: Open local page**

Visit:

```text
http://localhost:4173/
```

Expected: page renders with Chinese title and image cards; no obvious broken layout.

### Task 3: Prepare GitHub Repository

**Files:**
- Create: `.gitignore`

- [x] **Step 1: Create `.gitignore`**

Create:

```gitignore
.DS_Store
Thumbs.db
*.log
.vercel/
.env
.env.*
!.env.example
node_modules/
dist/
build/
```

- [x] **Step 2: Initialize git in project root**

Run:

```bash
cd "E:/NDM下载/山海经神兽全览/shanhaijing"
git init
git branch -M main
```

Expected: repository initialized with branch `main`.

- [x] **Step 3: Review files to be committed**

Run:

```bash
git status --short
```

Expected: only `index.html`, `images/`, `CLAUDE.md`, `.gitignore`, and `docs/` appear.

- [x] **Step 4: Commit**

Run:

```bash
git add .
git commit -m "chore: prepare static site deployment"
```

Expected: commit succeeds.

### Task 4: Publish to GitHub

**Files:**
- External GitHub repository

- [x] **Step 1: Check GitHub CLI auth**

Run:

```bash
gh auth status
```

Expected: logged in to GitHub. If not logged in, ask the user to run `! gh auth login`.

- [x] **Step 2: Create repository**

Run:

```bash
gh repo create shanhaijing-bestiaire --public --source "E:/NDM下载/山海经神兽全览/shanhaijing" --remote origin --push
```

Expected: GitHub repository is created and `main` is pushed.

- [x] **Step 3: Record repository URL**

Run:

```bash
gh repo view --web --json url -q .url
```

Expected: command prints the GitHub repository URL.

### Task 5: Deploy to Vercel

**Files:**
- External Vercel project

- [x] **Step 1: Check Vercel CLI**

Run:

```bash
vercel --version
```

Expected: Vercel CLI prints a version. If missing, use `npx vercel --version` or ask before installing globally.

- [x] **Step 2: Check Vercel auth**

Run:

```bash
vercel whoami
```

Expected: logged in. If not logged in, ask the user to run `! vercel login`.

- [x] **Step 3: Deploy production**

Run from project root:

```bash
cd "E:/NDM下载/山海经神兽全览/shanhaijing"
vercel --prod
```

Expected: command prints a production URL.

- [x] **Step 4: Verify deployment**

Open the production URL and confirm the page title contains `山海经异兽图鉴`.

Expected: deployed page renders successfully.

## Self-Review

- Spec coverage: covers project rules, local validation, GitHub publishing, Vercel deployment, and production verification.
- Placeholder scan: no placeholders or unresolved TODOs.
- Type consistency: not applicable; static site deployment only.

## Outcome

Completed on 2026-07-08.

- The repository was published at <https://github.com/ZSLPZDZH/shanhaijing-bestiaire>.
- The production site was deployed at <https://shanhaijing-alpha.vercel.app/>.
- The project remains a dependency-free static site served from the repository root.
