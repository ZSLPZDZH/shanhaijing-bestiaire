# Desktop Beast Detail Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Make every beast illustration fully visible in desktop detail dialogs while preserving the current mobile presentation.

**Architecture:** Keep the static single-file application and add two desktop-only responsive layers to the existing modal CSS. Medium desktop widths retain the stacked dialog with a contained image; wide desktops use a fixed-height two-column dialog with an independently scrolling text pane.

**Tech Stack:** HTML5, CSS media queries, vanilla JavaScript, Node.js standard library assertions, local static HTTP server, browser viewport testing

---

### Task 1: Add responsive desktop detail layouts

**Files:**
- Modify: `index.html:609`
- Reference: `docs/superpowers/specs/2026-07-17-desktop-beast-detail-design.md`

- [ ] **Step 1: Run a CSS contract check before implementation**

Run:

```bash
node --input-type=module -e "import { readFileSync } from 'node:fs'; const html = readFileSync('index.html', 'utf8'); const checks = [['medium desktop contains image', /@media \(min-width: 769px\)[\s\S]*?\.modal-image img\s*\{[\s\S]*?object-fit:\s*contain/], ['wide desktop uses columns', /@media \(min-width: 980px\)[\s\S]*?grid-template-columns:\s*minmax\(0, 1\.05fr\)\s*minmax\(420px, 0\.95fr\)/], ['wide desktop scrolls text', /@media \(min-width: 980px\)[\s\S]*?\.modal-body\s*\{[\s\S]*?overflow-y:\s*auto/]]; const missing = checks.filter(([, pattern]) => !pattern.test(html)).map(([name]) => name); if (missing.length) { console.error('Missing:', missing.join(', ')); process.exit(1); } console.log('Responsive modal CSS contract passed');"
```

Expected: exit code `1` with all three contracts reported as missing.

- [ ] **Step 2: Add the medium and wide desktop media queries**

Insert the following immediately before the existing `/* 响应式 */` block in `index.html`:

```css
        /* 桌面端详情：优先完整展示竖版神兽插图 */
        @media (min-width: 769px) {
            .modal-content {
                max-width: min(1120px, 100%);
            }

            .modal-image {
                height: clamp(360px, 60vh, 720px);
                overflow: hidden;
            }

            .modal-image img {
                object-fit: contain;
            }
        }

        @media (min-width: 980px) {
            .modal-content {
                display: grid;
                grid-template-columns: minmax(0, 1.05fr) minmax(420px, 0.95fr);
                height: min(86vh, 820px);
                max-height: 86vh;
                overflow: hidden;
            }

            .modal-image {
                height: 100%;
                min-height: 0;
                border-right: 1px solid var(--border);
            }

            .modal-body {
                min-height: 0;
                overflow-y: auto;
            }

            .modal-title-row {
                padding-right: 2.5rem;
            }
        }
```

- [ ] **Step 3: Re-run the CSS contract check**

Run the command from Step 1 again.

Expected: exit code `0` and `Responsive modal CSS contract passed`.

- [ ] **Step 4: Check formatting and scope**

Run:

```bash
git diff --check
git diff -- index.html
```

Expected: `git diff --check` produces no output; the diff changes only modal-related CSS in `index.html`.

- [ ] **Step 5: Commit the responsive layout**

```bash
git add index.html
git commit -m "fix(ux): show complete beasts on desktop"
```

Expected: one commit containing only `index.html`.

### Task 2: Verify desktop completeness and mobile regression

**Files:**
- Verify: `index.html`
- Verify: `beasts_data.json`
- Verify: `images/lushu.png`

- [ ] **Step 1: Start the static site**

Run:

```bash
python -m http.server 4173
```

Expected: the server listens on `http://127.0.0.1:4173/` and returns `200` for `index.html`, `beasts_data.json`, and `images/lushu.png`.

- [ ] **Step 2: Verify the wide desktop layout at 1440×900**

Open the site at a `1440×900` viewport, search for `鹿蜀`, open its card, and inspect the dialog.

Expected:

- the dialog has an image pane on the left and text pane on the right;
- the full image is visible from the sun and horse head through all four hooves and foreground rocks;
- scrolling the right pane does not move the left image;
- the close button remains visible.

- [ ] **Step 3: Verify responsive desktop boundaries**

Repeat at `1024×768`, `1920×1080`, and approximately `800×700`.

Expected:

- `1024×768` and `1920×1080` use the two-column layout and show the complete image;
- `800×700` uses the stacked layout, shows the complete image with dark side space, and allows the dialog to scroll to all text;
- no viewport introduces horizontal page scrolling.

- [ ] **Step 4: Verify the phone layout at 390×844**

Open the same entry at `390×844`.

Expected: the existing stacked phone layout remains in place, the image continues to fill its `300px`-high area, and the dialog scrolls normally.

- [ ] **Step 5: Verify interactions and browser errors**

At one desktop viewport, open and close the dialog using the close button, overlay click, and `Escape`. Inspect the browser console and failed network requests.

Expected: all three close methods work; there are no JavaScript errors and no local resource responses with status `404`.

- [ ] **Step 6: Record final repository state**

Run:

```bash
git status --short --branch
git log --oneline --decorate -3
```

Expected: the working tree is clean and the current branch is `codex/improve-desktop-beast-view`. Do not push the branch.
