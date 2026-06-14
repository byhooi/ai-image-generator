---
description: ai-image-generator 项目级 Claude Code 指令
---

# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目性质

- 纯前端 Cloudflare Pages 项目,无后端、无构建步骤、无包管理器(没有 `package.json`)、无测试套件、无 lint 配置。
- 不要建议 `npm install` / `npm run build` / `npm test` / `pytest` 等命令——它们在这里不存在。

## 文件结构

仓库根目录主要应用与配置文件:

- `index.html` — OpenAI 兼容接口的图片生成页面
- `gemini.html` — Gemini 图片生成页面
- `doubao.html` — 豆包(字节)图片生成页面
- `CNAME` — 旧 GitHub Pages 自定义域名文件;Cloudflare Pages 当前不依赖它,域名配置在 Cloudflare 控制台

每个 HTML 文件都是**自包含的**:内联 CSS + 内联 JavaScript,不互相引用。

## 三页并列约定

三个 HTML 页面是**功能并列**而非主从关系——它们共享同一套 UI 框架、同一套展馆逻辑、同一套 Tab 导航。修改时请注意:

- 顶部 Tab 导航链接、页脚、整体布局等**通用 UI**改动通常需要**同步到三个文件**,否则会出现不一致。
- 与具体 API 提供商相关(请求体、模型列表、响应解析、提供商专属参数)的逻辑只改对应文件即可。
- 改动前先用 Grep/Read 比对三个文件的相关片段,确认哪些是共享代码、哪些是各自特有。

## 本地运行与部署

- 本地预览:用浏览器直接打开 HTML 文件即可(双击或 `file://`),无需启动 dev server。涉及 CORS 时可起一个简易 http server。
- API 凭证由用户在页面 UI 中填入,持久化在 `localStorage`(`img_gen_settings`、`img_gen_profiles`),不需要 `.env`。
- 生成的历史图片放在 IndexedDB(`img-gen-gallery`)。
- 部署:`git push` 到 `main` 后由 Cloudflare Pages 自动发布。Cloudflare Pages 构建命令保持为空,发布目录指向仓库根目录,项目配置在 Cloudflare 控制台维护。

## UI 改动验证

代码里没有自动化测试。涉及 UI/交互改动时:

- 说清楚你做了什么,但不要声称已验证——除非你确实启动了浏览器在真实页面里操作过。
- 如果只能做静态分析,明确告诉用户"未在浏览器中验证"。

## 协作规范

- 全程使用中文回复,包括思维链。
- 提交信息沿用现有风格(短中文短语描述意图,例如"关闭豆包水印"、"调整展馆显示模型名的问题")。
- 近期迭代集中在**豆包页面**(分辨率、水印、兜底模型),改动这一区域时尤其要小心回归。
