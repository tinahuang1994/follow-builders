# 安装指南（小白版，5 步）

这个 skill 是 **fork-and-go**：你复制一份到自己名下、配一次 key，之后每天自动出一份「AI 日报小白版」deck。全程不用写代码。

## 1. Fork 这个仓库
打开本仓库页面 → 右上角 **Fork** → **Create fork**。现在它在你名下了。

## 2. 拿一个 twitterapi.io key（便宜，分钟级）
- 打开 **twitterapi.io** → **Get Your Free API Key** → 注册（邮箱或 Google，免信用卡）。
- 进 Dashboard，复制你的 **API Key**。
- 费用：按量 ~$0.15/千条推文，26 个人一天 ≈ 几分钱，一个月几块钱。

## 3. 把 key 存进仓库 Secret
你的 fork 页面 → **Settings**（仓库的，不是账号的）→ 左侧 **Secrets and variables → Actions** → **New repository secret**：
- Name：`TWITTERAPI_KEY`
- Secret：粘贴你的 key → **Add secret**

## 4. 打开 Actions（fork 默认是关的）
你的 fork → 顶部 **Actions** 标签 → 点 **“I understand my workflows, go ahead and enable them”**。
- 想立刻出第一份 feed：左侧选 **Generate Feeds** → **Run workflow**。
- 否则它每天 06:17 UTC 自动跑，把 `feed-x.json` 等更新回仓库。

## 5. 装 skill、答一个问题、收 deck
在 Claude 里安装这个 skill（设置 → Capabilities，或 `/follow-builders`）。它会问你「你是做什么的」——一句话即可。
之后每天它会读你仓库烤好的 feed，生成一份**讲到外行也能懂、且按你身份个性化**的小白版 HTML deck，存到你指定的文件夹。

---
**腾讯源**：默认开启（从搜狐镜像抓腾讯研究院 AI速递），不需要额外 key。
**播客**：v1 默认关闭（想开就配一个 `POD2TXT_API_KEY` secret）。
基于 [follow-builders by Zara Zhang](https://github.com/zarazhangrui/follow-builders)（MIT），见 `CREDITS.md`。
