# GitHub Project Introduction Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 为《山海经异兽图鉴》补齐产品型 README、真实页面预览和 GitHub 仓库元数据，让普通文化爱好者能快速理解并体验项目。

**Architecture:** 保持现有纯静态站点不变，只新增仓库展示层。README 引用一张从生产站点截取的 WebP 预览图；截图会话通过浏览器 DOM 临时隐藏旧数量口径，不改生产文件；GitHub About、Homepage 和 Topics 通过 GitHub CLI 更新并回读验证。

**Tech Stack:** Markdown, HTML/CSS/vanilla JavaScript, PowerShell, Chrome headless, FFmpeg, GitHub CLI, Vercel

---

## File Structure

- Create: `README.md` — 面向访问者的项目入口、能力说明、运行方式和内容边界。
- Create: `docs/assets/project-preview.webp` — README 使用的真实生产页面预览图。
- Existing: `beasts_data.json` — README 中数量、分类和字段说明的事实来源，不修改。
- Existing: `index.html` — 页面能力与在线验证对象，不修改。
- Existing: `CLAUDE.md` — 已在设计提交中声明 `README.md`、`docs/assets/` 和 Superpowers 文档目录，不再修改。
- External: `ZSLPZDZH/shanhaijing-bestiaire` repository metadata — 更新 About、Homepage 和 Topics。

### Task 1: Create the Production Preview Asset

**Files:**
- Create: `docs/assets/project-preview.webp`

- [ ] **Step 1: Create the documented asset directory**

Run from the repository root:

```powershell
New-Item -ItemType Directory -Path 'docs\assets' -Force | Out-Null
```

Expected: `docs/assets/` exists; `CLAUDE.md` already identifies it as documentation preview storage.

- [ ] **Step 2: Capture the real production page with corrected visible scope**

Start Chrome headless at `1440x1100` with remote debugging, open the production URL, then use CDP `Runtime.evaluate` before `Page.captureScreenshot`:

```javascript
for (const selector of ['.hero-stats', '.results-count']) {
  const element = document.querySelector(selector);
  if (!element) throw new Error(`Missing screenshot selector: ${selector}`);
  element.style.setProperty('display', 'none', 'important');
}
```

If hiding the counters leaves excessive space, the same temporary CDP session may reduce `.hero`'s `min-height`. Do not edit `index.html` or any production file.

Expected: CDP confirms both selectors were hidden, then creates `docs/assets/project-preview-source.png` at `1440x1100` showing the title, filter controls, and first row of beast cards without the stale count text.

- [ ] **Step 3: Convert and compress the screenshot**

Run:

```powershell
ffmpeg -y `
  -i 'docs\assets\project-preview-source.png' `
  -c:v libwebp `
  -quality 82 `
  'docs\assets\project-preview.webp'
```

Expected: exit code `0` and `docs/assets/project-preview.webp` exists.

- [ ] **Step 4: Verify the final asset before removing the source**

Run:

```powershell
$preview = Get-Item -LiteralPath 'docs\assets\project-preview.webp'
$probe = ffprobe -v error -select_streams v:0 `
  -show_entries stream=codec_name,width,height `
  -of json 'docs\assets\project-preview.webp' | ConvertFrom-Json

if ($preview.Length -le 0) {
  throw 'Preview image is empty.'
}

if ($probe.streams[0].codec_name -ne 'webp') {
  throw 'Preview image is not WebP.'
}

$probe.streams[0]
```

Expected: codec is `webp`, width is `1440`, height is `1100`, and the file is non-empty.

- [ ] **Step 5: Remove only the verified intermediate PNG**

Run:

```powershell
$sourcePreview = Resolve-Path -LiteralPath 'docs\assets\project-preview-source.png'
if ($sourcePreview.Path -notlike "$PWD\docs\assets\*") {
  throw "Unexpected preview path: $($sourcePreview.Path)"
}
Remove-Item -LiteralPath $sourcePreview.Path
```

Expected: the PNG is removed and `project-preview.webp` remains.

### Task 2: Add the Product-Focused README

**Files:**
- Create: `README.md`
- Use: `docs/assets/project-preview.webp`

- [ ] **Step 1: Create the complete README**

Use `apply_patch` to create `README.md` with exactly this content:

```markdown
<div align="center">

# 山海经异兽图鉴

可搜索、可分类浏览的《山海经》异兽数字图鉴，汇集 109 条图鉴记录，涵盖 89 个异兽名称及原文、释义与插图。

[**在线体验 →**](https://shanhaijing-alpha.vercel.app/)

</div>

![山海经异兽图鉴页面预览](docs/assets/project-preview.webp)

## 探索上古异兽

这个项目把《山海经》中的异兽整理成可浏览的数字图鉴。它适合传统文化爱好者快速查找异兽、阅读原文，并通过通俗解释理解古籍中的形态、地点与传说。

- **109 条图鉴记录**：涵盖 89 个异兽名称，以卡片形式集中浏览名称、拼音、地点与核心特征。
- **八类经文筛选**：覆盖南山经、西山经、北山经、东山经、中山经、海外经、海内经与大荒经。
- **全文搜索**：可按名称、拼音、原文和解释查找内容。
- **详情阅读**：展开查看原文、原文要点、通俗解释与插图。
- **响应式页面**：桌面端与移动端均可浏览，无需注册或安装应用。

## 数据内容

异兽数据集中保存在 [`beasts_data.json`](beasts_data.json)，每条记录包含：

当前数据文件共有109条记录，对应89个异兽名称；部分名称存在重复条目。

| 字段 | 内容 |
| --- | --- |
| `name` | 异兽名称 |
| `pinyin` | 名称拼音 |
| `category` | 所属经文分类 |
| `location` | 原文记载地点 |
| `original` | 《山海经》原文描述 |
| `key_points` | 形态、能力或象征要点 |
| `explanation` | 面向现代读者的通俗解释 |
| `image` | 对应插图的相对路径 |

这份数据适合文化浏览、创意参考和静态网页演示；它不是经过版本校勘的学术数据库。

## 技术实现

项目使用 HTML、CSS 和原生 JavaScript 构建，没有前端框架、后端、数据库或构建步骤。页面启动后读取 JSON 数据，在浏览器中完成搜索、筛选和详情展示。

```text
/
  index.html          # 页面结构、样式与交互
  beasts_data.json    # 异兽结构化数据
  images/             # 异兽插图
  docs/assets/        # 项目文档预览图
```

## 本地运行

```bash
git clone https://github.com/ZSLPZDZH/shanhaijing-bestiaire.git
cd shanhaijing-bestiaire
python -m http.server 4173
```

浏览器打开 <http://localhost:4173/>。

页面通过 `fetch` 读取 `beasts_data.json`，因此不要直接双击 `index.html`；本地 HTTP 服务可以避免浏览器的文件访问限制。

## 内容与版权说明

项目内容依据《山海经》原文整理，并提供面向现代读者的要点与通俗解释。古籍版本、异文与释义可能存在差异，本站内容仅用于传统文化学习、展示与交流，不应视为学术定本。

仓库当前未声明统一开源许可证。代码、整理文本和图片可能具有不同的权利边界；如需复制、改编或用于商业项目，请先确认对应内容的授权状态。
```

- [ ] **Step 2: Verify README facts against the data**

Run:

```powershell
$data = Get-Content -LiteralPath 'beasts_data.json' -Encoding utf8 -Raw | ConvertFrom-Json
$categories = @($data.category | Sort-Object -Unique)
$names = @($data.name | Sort-Object -Unique)
$readme = Get-Content -LiteralPath 'README.md' -Encoding utf8 -Raw

if ($data.Count -ne 109) {
  throw "Expected 109 data rows, found $($data.Count)."
}

if ($names.Count -ne 89) {
  throw "Expected 89 unique beast names, found $($names.Count)."
}

if ($categories.Count -ne 8) {
  throw "Expected 8 categories, found $($categories.Count)."
}

if (-not $readme.Contains('109 条图鉴记录') -or
    -not $readme.Contains('89 个异兽名称') -or
    -not $readme.Contains('八类经文筛选')) {
  throw 'README does not contain the verified collection counts.'
}

if ($readme -match '109\s+种异兽|109\s*只神兽') {
  throw 'README still contains a misleading collection count.'
}

$names.Count
$categories
```

Expected: unique name count is `89`, eight category names are printed, and no exception is raised.

- [ ] **Step 3: Verify README links and local asset paths**

Run:

```powershell
$requiredPaths = @(
  'README.md',
  'beasts_data.json',
  'docs\assets\project-preview.webp'
)

$missing = $requiredPaths | Where-Object { -not (Test-Path -LiteralPath $_) }
if ($missing) {
  throw "Missing README dependency: $($missing -join ', ')"
}

$response = Invoke-WebRequest `
  -Uri 'https://shanhaijing-alpha.vercel.app/' `
  -TimeoutSec 15 `
  -UseBasicParsing

if ($response.StatusCode -ne 200 -or $response.Content -notmatch '山海经异兽图鉴') {
  throw 'Production demo link is unavailable or points to the wrong page.'
}
```

Expected: no missing paths, HTTP status `200`, and the returned page contains `山海经异兽图鉴`.

- [ ] **Step 4: Check Markdown and whitespace**

Run:

```powershell
git diff --check -- README.md docs/assets/project-preview.webp
Select-String -LiteralPath 'README.md' `
  -Pattern 'TBD','TODO','权威完整收录' `
  -SimpleMatch
```

Expected: `git diff --check` reports no errors and `Select-String` returns no matches.

- [ ] **Step 5: Commit the README and preview atomically**

Run:

```powershell
git add -- README.md docs/assets/project-preview.webp
git diff --cached --check
git commit -m "docs(readme): introduce the bestiary project"
```

Expected: one commit creates the README and its referenced preview asset.

### Task 3: Validate the Static Site

**Files:**
- Verify: `index.html`
- Verify: `beasts_data.json`
- Verify: `images/`

- [ ] **Step 1: Verify every declared image exists**

Run:

```powershell
$data = Get-Content -LiteralPath 'beasts_data.json' -Encoding utf8 -Raw | ConvertFrom-Json
$missingImages = @(
  $data.image |
    Where-Object { $_ } |
    Sort-Object -Unique |
    Where-Object { -not (Test-Path -LiteralPath $_) }
)

if ($missingImages.Count -gt 0) {
  throw "Missing beast images: $($missingImages -join ', ')"
}

"Verified $(@($data.image | Where-Object { $_ } | Sort-Object -Unique).Count) image paths."
```

Expected: all unique image paths are verified and no exception is raised.

- [ ] **Step 2: Start a hidden local server and test site resources**

Run:

```powershell
$data = Get-Content -LiteralPath 'beasts_data.json' -Encoding utf8 -Raw | ConvertFrom-Json
$firstImage = [string](
  $data |
    Where-Object { -not [string]::IsNullOrWhiteSpace([string]$_.image) } |
    Select-Object -First 1
).image

if ([string]::IsNullOrWhiteSpace($firstImage)) {
  throw 'No non-empty image path exists in beasts_data.json.'
}

$existingListeners = @(
  Get-NetTCPConnection `
    -LocalAddress '127.0.0.1' `
    -LocalPort 4173 `
    -State Listen `
    -ErrorAction SilentlyContinue
)

if ($existingListeners.Count -gt 0) {
  throw 'Port 127.0.0.1:4173 is already in use; refusing to reuse an existing server.'
}

$server = $null

try {
  $server = Start-Process `
    -FilePath 'python' `
    -ArgumentList '-m','http.server','4173','--bind','127.0.0.1' `
    -WorkingDirectory $PWD `
    -WindowStyle Hidden `
    -PassThru

  $startupTimer = [System.Diagnostics.Stopwatch]::StartNew()
  $ready = $false

  while ($startupTimer.Elapsed -lt [TimeSpan]::FromSeconds(5)) {
    $server.Refresh()
    if ($server.HasExited) {
      throw "Local server process $($server.Id) exited before becoming ready."
    }

    $ownedListeners = @(
      Get-NetTCPConnection `
        -LocalAddress '127.0.0.1' `
        -LocalPort 4173 `
        -State Listen `
        -ErrorAction SilentlyContinue |
        Where-Object { $_.OwningProcess -eq $server.Id }
    )

    if ($ownedListeners.Count -gt 0) {
      $ready = $true
      break
    }

    Start-Sleep -Milliseconds 100
  }

  $startupTimer.Stop()

  if (-not $ready) {
    throw "Local server process $($server.Id) did not own 127.0.0.1:4173 within 5 seconds."
  }

  $index = Invoke-WebRequest -Uri 'http://127.0.0.1:4173/' -TimeoutSec 5 -UseBasicParsing
  $dataResponse = Invoke-WebRequest -Uri 'http://127.0.0.1:4173/beasts_data.json' -TimeoutSec 5 -UseBasicParsing

  $index.RawContentStream.Position = 0
  $indexReader = [System.IO.StreamReader]::new(
    $index.RawContentStream,
    [System.Text.UTF8Encoding]::new($false, $true),
    $true,
    1024,
    $true
  )
  try {
    $indexContent = $indexReader.ReadToEnd()
  } finally {
    $indexReader.Dispose()
  }

  if ($index.StatusCode -ne 200 -or $indexContent -notmatch '山海经异兽图鉴') {
    throw 'Local index page validation failed.'
  }

  $dataResponse.RawContentStream.Position = 0
  $dataReader = [System.IO.StreamReader]::new(
    $dataResponse.RawContentStream,
    [System.Text.UTF8Encoding]::new($false, $true),
    $true,
    1024,
    $true
  )
  try {
    $responseData = $dataReader.ReadToEnd() | ConvertFrom-Json
  } finally {
    $dataReader.Dispose()
  }

  if ($dataResponse.StatusCode -ne 200 -or @($responseData).Count -ne 109) {
    throw 'Local JSON data validation failed.'
  }

  if ([string]::IsNullOrWhiteSpace($firstImage)) {
    throw 'Image path is empty before the image request.'
  }

  $encodedImagePath = (
    $firstImage -split '[\\/]' |
      ForEach-Object { [Uri]::EscapeDataString($_) }
  ) -join '/'
  $imageResponse = Invoke-WebRequest `
    -Uri "http://127.0.0.1:4173/$encodedImagePath" `
    -TimeoutSec 5 `
    -UseBasicParsing

  if (
    $imageResponse.StatusCode -ne 200 -or
    $imageResponse.Headers['Content-Type'] -notmatch '^image/' -or
    $imageResponse.RawContentLength -le 0
  ) {
    throw 'Local image resource validation failed.'
  }

  "Server process $($server.Id) owns 127.0.0.1:4173; site, 109-row JSON data, and $firstImage passed validation."
} finally {
  if ($null -ne $server) {
    $server.Refresh()
    if (-not $server.HasExited) {
      Stop-Process -Id $server.Id -ErrorAction Stop -PassThru |
        Wait-Process -ErrorAction Stop
    }

    $server.Refresh()
    if (-not $server.HasExited) {
      throw "Local server process $($server.Id) still exists after cleanup."
    }

    if (Get-Process -Id $server.Id -ErrorAction SilentlyContinue) {
      throw "PID $($server.Id) was not released after cleanup."
    }
  }

  $remainingListeners = @(
    Get-NetTCPConnection `
      -LocalAddress '127.0.0.1' `
      -LocalPort 4173 `
      -State Listen `
      -ErrorAction SilentlyContinue
  )

  if ($remainingListeners.Count -gt 0) {
    throw 'Port 127.0.0.1:4173 is still listening after cleanup.'
  }
}
```

Expected: the newly started process owns the listener on `127.0.0.1:4173`; the site, 109-row JSON data, and a real non-empty `image/*` resource pass validation; `finally` stops the exact PID and confirms both the process and listener are released.

### Task 4: Update and Verify GitHub Repository Metadata

**Files:**
- External: GitHub About description
- External: GitHub Homepage
- External: GitHub Topics

- [ ] **Step 1: Record the current metadata**

Run:

```powershell
gh repo view ZSLPZDZH/shanhaijing-bestiaire `
  --json description,homepageUrl,repositoryTopics
```

Expected: the command returns the pre-change state for comparison.

- [ ] **Step 2: Update About and Homepage**

Run:

```powershell
gh api `
  --method PATCH `
  repos/ZSLPZDZH/shanhaijing-bestiaire `
  -f 'description=可搜索、可分类浏览的《山海经》异兽数字图鉴，汇集 109 条图鉴记录，涵盖 89 个异兽名称及原文、释义与插图。' `
  -f 'homepage=https://shanhaijing-alpha.vercel.app/'
```

Expected: the returned repository JSON contains the new `description` and `homepage`.

- [ ] **Step 3: Replace Topics with the approved set**

Run:

```powershell
$topics = @{
  names = @(
    'shanhaijing',
    'chinese-mythology',
    'chinese-culture',
    'bestiary',
    'digital-humanities',
    'static-website',
    'vanilla-javascript'
  )
} | ConvertTo-Json -Compress

$topics | gh api `
  --method PUT `
  -H 'Accept: application/vnd.github+json' `
  repos/ZSLPZDZH/shanhaijing-bestiaire/topics `
  --input -
```

Expected: the response contains exactly the seven approved topics.

- [ ] **Step 4: Re-read and assert the live metadata**

Run:

```powershell
$repo = gh repo view ZSLPZDZH/shanhaijing-bestiaire `
  --json description,homepageUrl,repositoryTopics | ConvertFrom-Json

$expectedDescription = '可搜索、可分类浏览的《山海经》异兽数字图鉴，汇集 109 条图鉴记录，涵盖 89 个异兽名称及原文、释义与插图。'
$expectedHomepage = 'https://shanhaijing-alpha.vercel.app/'
$expectedTopics = @(
  'bestiary',
  'chinese-culture',
  'chinese-mythology',
  'digital-humanities',
  'shanhaijing',
  'static-website',
  'vanilla-javascript'
)
$actualTopics = @($repo.repositoryTopics.name | Sort-Object)

if ($repo.description -ne $expectedDescription) {
  throw 'GitHub description does not match the approved design.'
}

if ($repo.homepageUrl -ne $expectedHomepage) {
  throw 'GitHub homepage does not match the verified production URL.'
}

if (Compare-Object $expectedTopics $actualTopics) {
  throw 'GitHub topics do not match the approved set.'
}

$repo | ConvertTo-Json -Depth 4
```

Expected: no exception is raised and the final metadata is printed.

### Task 5: Final Verification and Handoff

**Files:**
- Verify: `README.md`
- Verify: `docs/assets/project-preview.webp`
- Verify: Git history and working tree

- [ ] **Step 1: Run repository integrity checks**

Run:

```powershell
git diff --check
git status --short --branch
git log -3 --oneline
```

Expected: no whitespace errors, working tree is clean, and recent history contains the design, implementation plan, and README commits.

- [ ] **Step 2: Confirm no dependency or license was added**

Run:

```powershell
$unexpected = @(
  'package.json',
  'package-lock.json',
  'pnpm-lock.yaml',
  'yarn.lock',
  'LICENSE',
  'LICENSE.md'
) | Where-Object { Test-Path -LiteralPath $_ }

if ($unexpected) {
  throw "Unexpected dependency or license file: $($unexpected -join ', ')"
}

'No dependency manifest, lockfile, or license was added.'
```

Expected: the confirmation message is printed.

- [ ] **Step 3: Preserve the no-push boundary**

Do not run `git push`, create a remote branch, or merge into `main`. Report the local branch name and commit IDs so the user can explicitly authorize publishing later.

## Self-Review

- Spec coverage: Tasks 1–4 cover the preview, README structure, verified facts, copyright boundary, local validation, About, Homepage, and Topics.
- Scope: no site behavior, visual design, dependency, deployment, license, contribution workflow, or remote branch change is included.
- Placeholder scan: the plan contains no unresolved placeholders or deferred decisions.
- Command consistency: all repository files use relative paths; external targets use the exact GitHub repository and verified Vercel URL.
- Fact correction: all public-facing counts distinguish 109 data rows from 89 unique beast names; verification also checks 8 categories.
