# GitHub Project Introduction Design

## Goal

把仓库首页从“只有源码文件”升级为面向普通文化爱好者的产品入口，让第一次访问的人在短时间内理解项目价值、打开在线图鉴，并知道内容范围和使用边界。

## Positioning

项目定位为“可在线探索的《山海经》异兽数字图鉴”，而不是开发框架、通用组件库或可直接再授权的数据集。

核心信息：

- 收录 109 种异兽；
- 覆盖南山经、西山经、北山经、东山经、中山经、海外经、海内经、大荒经八类；
- 支持名称与描述搜索、按经文分类筛选、卡片浏览和详情阅读；
- 每条内容包含名称、拼音、出处分类、地点、原文、要点、通俗解释和插图字段；
- 在线体验地址为 <https://shanhaijing-alpha.vercel.app/>。

## Repository Metadata

GitHub About 使用以下描述：

> 可搜索、可分类浏览的《山海经》异兽数字图鉴，收录 109 种异兽及原文、释义与插图。

Homepage 设置为：

> https://shanhaijing-alpha.vercel.app/

Topics 设置为：

- `shanhaijing`
- `chinese-mythology`
- `chinese-culture`
- `bestiary`
- `digital-humanities`
- `static-website`
- `vanilla-javascript`

## README Information Architecture

`README.md` 采用产品型结构，按用户决策顺序组织：

1. 项目名称、简短定位和“在线体验”入口；
2. 一张来自真实生产页面的预览图；
3. 核心能力：109 种异兽、八类经文、搜索筛选、详情阅读；
4. 数据字段说明与适合的使用场景；
5. 技术实现与目录结构；
6. 最短本地运行步骤；
7. 内容来源、准确性声明和版权边界。

README 不添加无实际价值的徽章，不写虚假的构建状态、版本号、下载量或“权威完整收录”等无法验证的表述。

## Preview Asset

从当前生产站点截取桌面端真实页面，保存为 `docs/assets/project-preview.webp`，在 README 中使用仓库相对路径引用。

预览图只承担“让用户快速判断页面风格和内容”的作用，不对网页本身做视觉或功能修改。图片应压缩到适合 GitHub 加载的体积，同时保持标题、筛选栏和首屏卡片可辨认。

## Content and Licensing Boundary

当前仓库没有 `LICENSE`，图片和整理文本的完整授权来源也未在仓库中明确。此次不自动添加 MIT 或其他开源许可证，避免让访问者误以为代码、图片和内容均可自由再授权。

README 将明确：

- 项目用于传统文化学习、展示与交流；
- 内容依据《山海经》原文整理，并包含现代通俗解释；
- 古籍版本、异文和释义可能存在差异，不把本站表述为学术定本；
- 图片与整理内容的再使用应先确认相应权利边界。

## Project Rules

新增 `docs/assets/` 前，先在 `CLAUDE.md` 中声明其用途；`docs/superpowers/specs/` 和 `docs/superpowers/plans/` 分别保存已确认设计和实施计划。

项目继续保持纯静态 HTML、CSS 和原生 JavaScript，不引入依赖，不改动页面功能。

## Verification

实施完成后验证：

- README 中的在线体验链接返回成功，页面标题包含“山海经异兽图鉴”；
- README 相对图片路径可解析，预览图存在且可正常打开；
- README 中的数量与 `beasts_data.json` 一致；
- GitHub About、Homepage 和 Topics 与本设计一致；
- 本地执行 `python -m http.server 4173` 后，站点、数据和图片资源仍能正常加载；
- 仓库不新增第三方依赖、密钥或许可证。

## Out of Scope

- 修改站点交互、文案或视觉设计；
- 新增框架、构建工具或自动化部署；
- 添加贡献指南、Issue 模板、路线图或 Wiki；
- 在未确认权利来源前添加开源许可证；
- 自动推送分支或合并到 `main`。
