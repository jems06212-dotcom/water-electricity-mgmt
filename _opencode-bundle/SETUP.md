# 🏠↔🏢 雙地工作 OpenCode 設定指南

## 情境

- **A 機器（家裡筆電）**：已有完整 OpenCode + Codex + 技能
- **B 機器（公司電腦）**：全新安裝，要接續 A 的工作

## 事前檢查

OpenCode 本體需要手動安裝（沒有雲端版），到公司後要重新裝一次。

> ✅ 專案檔案在 GDrive 會自動同步
> ✅ Obsidian vault 在 GDrive 會自動同步
> ✅ GitHub 記錄所有版本歷史

---

## Step 1 — 安裝 OpenCode

在 B 機器上，先安裝 OpenCode：

```powershell
npm install -g opencode
```

確認版本：
```powershell
opencode --version
```

---

## Step 2 — 安裝懶人包技能

複製貼上這段，一次性安裝所有懶人包技能：

```powershell
# 建立技能目錄
mkdir -p "$env:USERPROFILE\.agents\skills\claude-code-lazy-packs\skills"
cd "$env:USERPROFILE\.agents\skills\claude-code-lazy-packs"
git init
git remote add origin https://github.com/mathruffian-dot/claude-code-lazy-packs
git pull origin main
```

> 如果 git pull 失敗，技能也可以手動從 A 機器複製：
> - 來源（A）：`C:\Users\jems0\.agents\skills\claude-code-lazy-packs\`
> - 目的地（B）：`C:\Users\<B的帳號>\.agents\skills\claude-code-lazy-packs\`

---

## Step 3 — 放 CLAUDE.md（最重要）

**這是 Sisyphus 的靈魂檔案**，決定 OpenCode 開機後的行為模式。

```powershell
# 確保你有 OpenCode 工作目錄
mkdir -p D:\OPENCODE_0621
```

把 `_opencode-bundle\CLAUDE.md` 複製到 `D:\OPENCODE_0621\CLAUDE.md`。

> 位置必須跟 A 機器一樣是 `D:\OPENCODE_0621\CLAUDE.md`，否則 Sisyphus 找不到設定。
>
> 如果你的 D 槽結構不同，可以放在別的地方，但要確保 OpenCode 啟動時的工作目錄是那一層。

---

## Step 4 — 放 .omo 設定

`_opencode-bundle\.omo\` 包含排程器、協議、任務定義，全部複製到 `D:\OPENCODE_0621\.omo\`。

```powershell
# 從 _opencode-bundle 複製 .omo 到 OpenCode 工作目錄
Copy-Item -Path "D:\googole driver\水電管理\_opencode-bundle\.omo" -Destination "D:\OPENCODE_0621\" -Recurse -Force
```

---

## Step 5 — 登入 GitHub

```powershell
gh auth login
```

選 **HTTPS** → **Login with a web browser** → 貼上 one-time code 認證。

確認成功：
```powershell
gh auth status
```

---

## Step 6 — 設定 Codex（如果有用 Codex）

如果 B 機器也有裝 Codex，把以下規則加到 `%USERPROFILE%\.codex\AGENTS.md`：

```markdown
回答問題時都要用繁體中文。

開專案時先幫我環境檢測，只在開始檢測一次，發現異常請回報給我

開專案或進入專案資料夾時，環境檢測要先參考：
D:\codex\01.5-Codex必裝Skills與Plugins.md

# 第二大腦（與 OpenCode 同步）
- Obsidian vault 路徑：`D:\googole driver\my-vault`

# 水電管理專案
- 專案路徑：`D:\googole driver\水電管理`
- 專案 GitHub：`https://github.com/jems06212-dotcom/water-electricity-mgmt`
- 工作筆記：`D:\googole driver\my-vault\水電管理\工作筆記.md`

# 工作規則（與 OpenCode 同步）
- OpenCode 專案路徑：`D:\OPENCODE_0621`
```

---

## Step 7 — 開始工作

```powershell
# 確保 GDrive 同步完成
ls "D:\googole driver\水電管理"

# 進入專案
cd "D:\googole driver\水電管理"

# 打開 OpenCode
opencode
```

在 OpenCode 中說：**「開工」**

我會自動：
1. 讀 Obsidian 工作筆記 → 告訴你上次做到哪
2. 檢查 git 遠端狀態 → 建議 git pull
3. 讓你決定方向

---

## 每日工作節奏

```
🏠 在家：
  工作 → 說「收工」→ 自動 commit + push + 更新工作筆記

🏢 在公司：
  進專案 → 說「開工」→ git pull → 接續上次進度 → 工作 → 說「收工」

🏠 回家：
  說「開工」→ git pull → 接續工作...
```

### 要點

| 動作 | 時機 |
|------|------|
| **git pull** | 每次換機器後第一次開工時做 |
| **說「收工」** | 每次結束開發前，確保 commit + push |
| **留意 GDrive 同步** | 換機器前確認 GDrive 圖示已完成同步（綠色勾） |
| **不要同時編輯** | 同一台機器關掉 OpenCode 再換另一台，避免衝突 |
