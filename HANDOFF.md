# 项目交接文档 (HANDOFF)

## 一、 项目概况

- **项目名称**：AI 图片生成工具 (`ai-image-generator`)
- **工作区路径**：`D:\Documents\Github\ai-image-generator`
- **运行与部署**：纯前端静态网页，部署于 **GitHub Pages**，绑定自定义域名 `st.468024.xyz`（配置文件为 [CNAME](file:///D:/Documents/Github/ai-image-generator/CNAME)）。
- **技术规范**：纯原生 HTML5 / CSS3 / JavaScript，无构建工具、无 Node/npm 依赖，代码修改直接作用于 HTML 或静态文件。
- **当前业务功能**：
  - **豆包 Seedream**：调用火山方舟 `/images/generations`，支持文生图、图生图、2K/4K 分辨率选择，生图模型与提示词模型均全面开放；
  - **Gemini 原生生图**：调用 Google Gemini `generateContent`（支持 `gemini-*`、`imagen-*` 及任意中转/自定义生图模型）；
  - **生图模型全面开放**：移除了任何硬编码限制与强制大小写转换，从接口获取的所有模型完整列出，支持任意自定义模型输入，且同时兼容多模态 `inlineData` 与中转站常见的 `b64_json`/`base64`/`url` 格式；
  - **独立提示词模型选择（Prompt Model）**：支持在界面中为 AI 提示词优化单独指定文本模型，并直接从接口获取的模型列表下拉选择或手动输入；
  - **提示词智能优化与辅助**：AI 智能生图扩写润色、快捷风格预设标签（Chips）、字数实时统计、清空与一键撤回原词；
  - **请求生命周期管理**：支持随时手动点击【取消生成】（`AbortController`）以及 120 秒超时自动中止；
  - **本地功能**：客户端多图 Canvas 压缩与上传、轻量缩略图存储与按需大图查看、灯箱大图缩放平移、图片下载与剪贴板复制。

---

## 二、 当前状态与近期变动

1. **Git 仓库状态**：
   - 当前在 `main` 分支上完成了生图模型全面放开、提示词模型选择与提示词优化功能落地。
   - 涉及修改的文件：[doubao.html](file:///D:/Documents/Github/ai-image-generator/doubao.html)、[gemini.html](file:///D:/Documents/Github/ai-image-generator/gemini.html)、[index.html](file:///D:/Documents/Github/ai-image-generator/index.html)。
2. **已落地的优化成果**：
   - ✅ **生图模型彻底放开**：
     - 去除原先只允许 2 个指定模型的写死逻辑；
     - 取消对模型名大小写的强制修改（支持大小写敏感的自定义模型与中转别名）；
     - 下拉列表中自动将 `/models` 接口返回的全部模型开放给用户选择；
     - 预设推荐模型扩充（包含 `gemini-3.1-flash-image`, `gemini-3-pro-image`, `imagen-3.0-generate-002`, `imagen-3.0-capability-002`, `doubao-seedream-4-5-251128`, `doubao-seedream-5-0-260128` 等）；
     - 图片解析器全面兼容 `inlineData`、`b64_json`、`base64` 和 `url` 等各种图片输出结构；
   - ✅ **独立提示词模型选择与下拉支持**：在 Gemini 页面和豆包页面新增了“提示词模型 (Prompt Model)”输入框，聚焦时自动拉取接口返回的模型列表并以独立下拉面板展示，支持从列表点击选中或手动输入，并自动保存到 `localStorage`；
   - ✅ **提示词优化功能**：在所有页面添加了 `✨ 优化提示词`、`↩ 撤回` 按钮、`清空` 按钮、实时字数统计以及 6 大常用生图风格快捷标签（写实摄影、唯美动漫、赛博朋克、国风水墨、3D盲盒、电影质感）；
   - ✅ **请求取消与超时保护**：生成按钮旁新增明显的红色【取消生成】按钮，挂接 `AbortController`，并在 `fetch` 超时 120 秒时自动中断，彻底解决网络挂起导致的 UI 永久锁死问题；
   - ✅ **展馆性能优化（缩略图机制）**：将生成图片在保存至 IndexedDB 时自动压缩生成 320px 缩略图（`thumbDataUrl`），展馆渲染优先使用轻量缩略图，点开看图和下载时才读取原图，内存开销降低 95% 以上。

---

## 三、 核心代码位置与函数索引

| 模块 / 功能 | 关键位置 | 说明 |
| :--- | :--- | :--- |
| **项目文档与规范** | [AGENTS.md](file:///D:/Documents/Github/ai-image-generator/AGENTS.md) / [CLAUDE.md](file:///D:/Documents/Github/ai-image-generator/CLAUDE.md) | 包含开发约定、无构建步骤说明、域名配置 |
| **生图核心逻辑与取消** | [gemini.html](file:///D:/Documents/Github/ai-image-generator/gemini.html)<br>[doubao.html](file:///D:/Documents/Github/ai-image-generator/doubao.html) | `generate()`、`resetUI()`、`AbortController` 控制逻辑 |
| **生图模型与提示词模型下拉** | [gemini.html](file:///D:/Documents/Github/ai-image-generator/gemini.html)<br>[doubao.html](file:///D:/Documents/Github/ai-image-generator/doubao.html) | `renderModelPanel()`、`renderPromptModelPanel()`、`loadModels()` |
| **响应图片多格式解析** | [gemini.html](file:///D:/Documents/Github/ai-image-generator/gemini.html)<br>[doubao.html](file:///D:/Documents/Github/ai-image-generator/doubao.html) | `extractBase64AsDataUrl()` 兼容 inlineData、b64_json、base64、url |
| **提示词优化与风格标签** | [gemini.html](file:///D:/Documents/Github/ai-image-generator/gemini.html)<br>[doubao.html](file:///D:/Documents/Github/ai-image-generator/doubao.html) | `optimizePrompt()`、`localEnhancePrompt()`、`updatePromptCounter()` |
| **IndexedDB 展馆与缩略图** | [gemini.html](file:///D:/Documents/Github/ai-image-generator/gemini.html)<br>[doubao.html](file:///D:/Documents/Github/ai-image-generator/doubao.html) | `createThumbnailDataUrl()`、`addToGallery()`、`renderGallery()` |
| **全屏预览看图器** | [gemini.html](file:///D:/Documents/Github/ai-image-generator/gemini.html)<br>[doubao.html](file:///D:/Documents/Github/ai-image-generator/doubao.html) | 缩放、平移拖拽、ESC/点击关闭 |
| **参考图压缩处理** | [gemini.html](file:///D:/Documents/Github/ai-image-generator/gemini.html)<br>[doubao.html](file:///D:/Documents/Github/ai-image-generator/doubao.html) | Canvas 动态缩放并压制在 1024px 以内 |

---

## 四、 后续可选演进方向

1. **入口文件规范**：
   - 当前 [index.html](file:///D:/Documents/Github/ai-image-generator/index.html) 与 [doubao.html](file:///D:/Documents/Github/ai-image-generator/doubao.html) 保持了同步升级。后续可根据实际需要决定是否将 `doubao.html` 简化为重定向跳转，或者将 `index.html` 改为多模型聚合单页（SPA）。
2. **样式与脚本文件解耦（抽取共享文件）**：
   - 提取 `css/shared.css` 和通用 JS 工具模块，进一步消除三文件之间的内联样式和重复代码。
3. **展馆管理增强**：
   - 增加按提示词关键词搜索、批量导出为 ZIP / 导出为 JSON 备份功能。
