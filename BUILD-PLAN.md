# AI 日报小白版 · Build Plan（Tina 的 fork 改造计划）

> 基于 [follow-builders by Zara Zhang](https://github.com/zarazhangrui/follow-builders)（MIT）改造。
> 保留她的 LICENSE 与署名；下面是我们在她代码上加的东西。

## 原版架构（已读懂）
- **烤 feed（maintainer 端）**：`.github/workflows/generate-feed.yml` 每天 06:17 UTC 跑 `scripts/generate-feed.js`：
  - X：**官方 X API**（`api.x.com/2`，需 secret `X_BEARER_TOKEN`）← 贵（~$200/月）
  - 播客：RSS + pod2txt 转录（需 secret `POD2TXT_API_KEY`）
  - 博客：免费爬（Anthropic Engineering、Claude Blog）
  - 提交 `feed-x.json / feed-podcasts.json / feed-blogs.json` 回 repo
- **读 feed（用户端）**：`scripts/prepare-digest.js` 读 3 个 feed + `prompts/` + 用户 config → 输出一坨 JSON
- **remix**：agent 按 `prompts/` 把 JSON 组织成文字 digest → `deliver.js` 送达（stdout/telegram/email）
- 源清单：`config/default-sources.json`（26 个 X builder + 6 播客 + 2 官博）

## 我们的改造（5 块）
1. **X 改便宜源**：把 `generate-feed.js` 里 `fetchXContent` 从官方 X API 换成 **twitterapi.io**（按量 ~$0.15/千条，~$2–3/月）。
   - 新 secret：`TWITTERAPI_KEY`（替代 `X_BEARER_TOKEN`）。
2. **加腾讯源**：`generate-feed.js` 新增 `fetchTencent()`（爬腾讯研究院 AI速递 / 搜狐 455313），产出 `feed-tencent.json`。跑在 GitHub runner（网络不受限，爬取没问题）。
3. **产出改小白版 deck**：新增 `prompts/make-deck.md` 定义 960×540 中文小白版 HTML deck 格式；改 `SKILL.md` 送达步骤＝生成 HTML deck 文件（不是发文字）。
4. **个性化 So-what**：`config-schema.json` 加 `userContext`（"你是做什么的"）；onboarding 加一问；deck 每条加「So what · 对你」按该用户定制。
5. **署名 + 改名**：保留/补 `LICENSE`（MIT，署 Zara + Tina）；README 顶部写 "based on follow-builders by Zara Zhang (MIT)"；skill 改名为 AI 日报小白版。

## Tina 需要做的（setup）
- [ ] 注册 **twitterapi.io** 拿 key → 加到 repo 的 **Settings → Secrets → Actions**，名字 `TWITTERAPI_KEY`。（注册送 $1 够测很久。）
- [ ] （可选）保留播客的话，pod2txt 拿 key → secret `POD2TXT_API_KEY`；不想弄就砍掉播客。
- [ ] 在 GitHub repo 打开 Actions（fork 默认可能禁用）。
- key 永远只进 repo secret，不发给我。

## 已定（2026-06-30）
- 名字：**AI 日报小白版**。
- 播客：**v1 砍掉**（少一个 key、省 token；以后一行加回）。源 = X + 腾讯 + 官博。

## 进度
- [x] `prompts/make-deck.md`：小白版 deck 格式 + 个性化 So-what 规格
- [x] `config/config-schema.json`：加 `userContext` + `outputDir`
- [x] `CREDITS.md`：署名 Zara（MIT）
- [x] `generate-feed.js`：X 换 twitterapi.io（端点 /twitter/user/last_tweets，header X-API-Key；env `TWITTERAPI_KEY`）✅ 语法已过
- [x] `.github/workflows/generate-feed.yml`：secret 改 `TWITTERAPI_KEY`；播客 key 缺失时优雅跳过
- [x] `prepare-digest.js`：feed/prompt 指向 **tinahuang1994** 的 fork；加 feedTencent；prompts 换成 make-deck；config 透传 userContext/outputDir
- [ ] `generate-feed.js`：加 `fetchTencent()` + `feed-tencent.json`（搜狐没法在沙箱测，需首次 Action 跑出来验证）
- [ ] `SKILL.md`：onboarding 加「你是做什么的」；送达步骤改成生成 deck；改名 + 去掉 telegram/email 复杂度
- [ ] 砍播客相关代码/配置

## ⚠️ 上线前的关键一步：把改动推进 GitHub fork
现在所有改动只在桌面那份 `follow-builders-main`，**还没进你的 GitHub 仓库**——所以 Action 还跑的是原版。建完代码后要把这个文件夹的改动 push 到 `tinahuang1994/follow-builders`（推荐用 GitHub Desktop 这种图形工具，最省事），Action 才会用新代码。secret `TWITTERAPI_KEY` 已加好。
