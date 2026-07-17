# 山海经异兽图鉴 项目规则

## 项目定位

这是一个纯静态中文网页项目，展示《山海经》异兽图鉴。项目根目录为本目录 `shanhaijing/`，不是上一层 `山海经神兽全览/`。

## 技术栈

- HTML / CSS / 原生 JavaScript
- 静态图片资源位于 `images/`
- 无构建步骤、无后端、无数据库

## 目录约定

```text
/
  README.md                 # GitHub 项目介绍与在线体验入口
  index.html                # 单页应用入口，包含页面结构、样式和交互脚本
  beasts_data.json          # 异兽结构化数据
  images/                   # 异兽图片资源，文件名使用英文/拼音小写
  docs/assets/              # README 等项目文档使用的预览图
  docs/superpowers/specs/  # 已确认的设计规格
  docs/superpowers/plans/  # 可执行的实施计划
  CLAUDE.md                 # 项目级工作规则
```

## 开发约定

- 保持纯静态站点，不引入框架，除非出现明确的第二个页面或复杂状态管理需求。
- 图片引用必须使用相对路径 `images/<name>.png`。
- 不保留无用文件、重复图片或临时导出文件。
- 中文正文、标题和 UI 文案保持简体中文。
- 代码注释只解释设计意图或非显而易见的原因，不解释语法。

## 验证命令

本项目无构建命令。发布前必须做以下验证：

```bash
python -m http.server 4173
```

然后访问：

```text
http://localhost:4173/
```

同时检查：

- `index.html` 能正常加载；
- 控制台无资源 404；
- `images/` 中被引用的图片都存在；
- 页面在桌面浏览器中可滚动和交互。

## GitHub 发布

- 仓库根目录应为本目录 `shanhaijing/`。
- 不要把上一层重复的 `images/` 目录提交进仓库。
- commit message 使用英文，格式示例：`chore: prepare static site deployment`。

## Vercel 部署

- 使用 Vercel 静态站点部署。
- Framework Preset 选择 `Other` 或自动识别静态项目。
- Build Command 留空。
- Output Directory 留空或使用项目根目录。
