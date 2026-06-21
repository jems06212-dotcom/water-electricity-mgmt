# 開工協議 — Sisyphus 接續工作流程

## 觸發條件

使用者說出以下任一關鍵字，**立即執行此協議**（不等使用者重複）：

| 關鍵字 | 說明 |
|--------|------|
| 開工、開始工作 | 新工作階段開始 |
| 我來了、上工 | 使用者回到工作 |
| 上次做到哪、我們繼續、接下來呢、接續工作、來吧、開始 | 請求接續先前進度 |

## 執行步驟

### 1. 載入技能
立即載入 `cc-startup` skill（從 `~/.agents/skills/claude-code-lazy-packs/skills/10-startup/SKILL.md`）。

### 2. 確認工作目錄
```powershell
$repoName = (Get-Item $PWD).Name
```
確認 `.git` 存在，否則提示「目前目錄不是 git repo，無法執行開工 SOP」。

### 3. 讀取工作筆記
```powershell
$vaultPath = "D:\googole driver\my-vault"
$notePath = "$vaultPath\$repoName\工作筆記.md"
```
- 優先使用 Obsidian MCP 工具讀取
- 若不可用，直接讀取檔案系統路徑
- 若筆記不存在，告知使用者「尚未建立對應的工作筆記，建議先初始化專案」

### 4. 檢查 git 狀態
```powershell
git status --short                     # 本地變動
git fetch origin --quiet               # 更新遠端資訊
$behind = git rev-list HEAD..origin/HEAD --count 2>$null  # 落後數量
```

### 5. 摘要回報
```
📂 專案：<REPO_NAME>
📘 上次做到哪：<摘要>
🔧 本地 git：<clean / N files changed>
🌐 遠端：<最新 / 落後 N commits>

➡️ 建議下一步：
1. ...
2. ...

要從哪個方向開始？
```

## 注意事項

- **不主動 pull**（避免蓋掉未 commit 的本地變動）
- **不修改工作筆記**（那是收工協議的事）
- **摘要要精簡**，不要全文倒出 Obsidian 筆記
