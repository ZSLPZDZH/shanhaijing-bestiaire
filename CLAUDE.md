# 山海经异兽图鉴 项目规则

## 项目定位

这是一个纯静态中文网页项目，展示《山海经》异兽图鉴。仓库根目录为当前 `shanhaijing-bestiaire/` 目录，不要把任何上层素材目录当作部署根目录。

## 技术栈

- HTML / CSS / 原生 JavaScript
- 静态图片资源位于 `images/`
- 无构建步骤、无后端、无数据库

## 目录约定

```text
/
  AGENTS.md                 # Codex 项目入口，指向本文件
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
- 控制台无 JavaScript 错误，页面显式引用的本地资源无 404；
- `images/` 中被引用的图片都存在；
- `769px` 至 `979px` 视口中详情图完整显示，弹窗滚动时关闭按钮保持可见；
- `980px` 及以上视口中详情弹窗为左图右文双栏，右侧文字可独立滚动；
- `768px` 及以下视口继续使用移动端详情布局，关闭按钮、遮罩点击和 `Escape` 均可关闭弹窗。

## GitHub 发布

- 仓库根目录应为本目录 `shanhaijing-bestiaire/`。
- 不要把上一层重复的 `images/` 目录提交进仓库。
- commit message 使用英文并表达单一意图，格式示例：`fix(ux): show complete beasts on desktop`。

## Vercel 部署

- 使用 Vercel 静态站点部署。
- Framework Preset 选择 `Other` 或自动识别静态项目。
- Build Command 留空。
- Output Directory 留空或使用项目根目录。
- 生产地址为 <https://shanhaijing-alpha.vercel.app/>；合并到 `main` 后必须回读生产页面确认目标改动已生效。
