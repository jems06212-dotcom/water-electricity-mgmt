# 水電管理專案 — Codex 專案規則

## 基本規則

- 回答一律使用繁體中文。
- 本專案與 OpenCode（Sisyphus）雙向同步，GitHub 為 source of truth。

## 專案資訊

- 專案路徑：本專案所在目錄（GDrive 內）
- GitHub repo：`https://github.com/jems06212-dotcom/water-electricity-mgmt`
- Obsidian 工作筆記：`<GDrive路徑>/my-vault/水電管理/工作筆記.md`（位置依各機器 GDrive 路徑而定）
- 開源類型：公開 repo

## 日常流程

### 開始工作
1. `git pull origin master` — 取得最新進度（包含家中 OpenCode 的更新）
2. 讀取 `工作筆記.md` — 確認上次做到哪
3. 開始工作

### 結束工作
1. 更新 `工作筆記.md` 的「上次做到哪」及「最近更動紀錄」
2. `git add .`
3. `git commit -m "feat: 清楚的 commit 訊息"`
4. `git push origin master`

## 注意事項

- **不要 commit** `.env`、credentials、`node_modules/`、`.claude/`、`.codex/`
- commit message 要寫清楚做了什麼 + 為什麼
- 跟 OpenCode（Sisyphus）協作時，需要給 Sisyphus 看的回報可寫在 `_opencode-bridge/outbox/`（若橋接目錄存在）
- 若 GDrive 路徑跟預設不同，請自行更新上述路徑
