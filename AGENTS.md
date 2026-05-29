# Repository Guidelines

## 项目结构与模块组织

本仓库是部署在 GitHub Pages 上的静态 AI 图片生成工具，不需要后端服务或构建流程。主要文件如下：

- `index.html`：主图片生成页面。
- `gemini.html`：Gemini 图片生成页面，包含模型列表、模型校验和旧模型兼容逻辑。
- `doubao.html`：豆包图片生成页面。
- `CNAME`：GitHub Pages 自定义域名配置。
- `CLAUDE.md`：本地助手协作说明，不属于应用代码。

当前没有独立的 `src/`、`tests/` 或 assets 目录。修改时应优先限制在相关 HTML 文件内，只有在确实需要复用资源或配置时再新增目录。

## 构建、测试与本地开发命令

项目没有包管理器和构建步骤。快速检查可直接打开页面：

```powershell
Start-Process .\index.html
```

如需模拟 GitHub Pages 的访问方式，可启动本地静态服务：

```powershell
python -m http.server 8000
```

然后访问 `http://localhost:8000/index.html`、`gemini.html` 或 `doubao.html`。

提交前建议执行：

```powershell
git diff --check
git diff
```

## 编码风格与命名规范

使用原生 HTML、CSS 和 JavaScript。延续现有单文件结构：样式放在 `<style>`，页面结构放在 `<body>`，逻辑放在 `<script>`。缩进使用四个空格。

JavaScript 变量和函数使用描述性的 camelCase 命名，例如 `loadModels`、`normalizeModelId`、`buildPayload`。模型 ID、localStorage key 和 API 地址应集中维护，避免散落在多个逻辑分支中。

修改供应商相关逻辑时避免大范围重构。界面文案、代码注释和项目文档应使用中文，并保持与现有页面一致的语气。

## 测试指南

当前没有自动化测试。改动后请在浏览器中手动验证：

- 页面加载后控制台无错误。
- 供应商配置可以保存和恢复。
- 校验路径可正常工作，包括缺少 API Key、无效模型、空提示词。
- UI 改动需检查桌面宽度和窄屏移动端宽度。

如果后续引入自动化测试，请放入 `tests/` 目录，并在本文档补充运行命令和测试约定。

## 提交与 Pull Request 规范

近期提交历史主要使用简短的 `feat:` 和 `fix:` 前缀，也包含中文说明。建议使用简洁的 Conventional Commit 风格：

```text
feat: 更新 Gemini 图片模型 ID
fix: 兼容已保存的过时模型值
```

Pull Request 应包含变更摘要、影响的页面、手动测试结果；涉及可见 UI 变化时请附截图。有关联 issue 时应在描述中链接。

## 安全与配置建议

不要提交 API Key、访问令牌或个人配置。API Key 应保持由用户在浏览器中输入，并仅通过现有 localStorage 配置流程保存。
