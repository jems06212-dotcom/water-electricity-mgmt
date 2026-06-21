# 收工協議 — Sisyphus 結束工作流程

## 觸發條件

使用者說出以下任一關鍵字，**立即執行此協議**：

| 關鍵字 | 說明 |
|--------|------|
| 收工、完工 | 正常結束工作 |
| 結束了、先到這裡、今天就這樣 | 提前結束 |
| 下班、打包、準備關機、差不多了 | 離開工作 |
| 該同步的同步 | 只同步不結束 |

## 執行步驟

### 1. 載入技能
立即載入 `cc-shutdown` skill（從 `~/.agents/skills/claude-code-lazy-packs/skills/11-shutdown/SKILL.md`）。

### 2. 盤點工作成果
從當前對話記錄摘要：
- 完成了哪些檔案 / 功能
- 做了哪些決策（重要的架構或設計決定）
- 踩到哪些坑（需記錄到踩坑筆記）

### 3. 更新 Obsidian 工作筆記

讀取現有工作筆記 → 更新三個區塊：

**⏯️ 上次做到哪：**
```
**最後動作**：<今天完成的工作摘要>
**所在 repo**：[<repo名>](<repo URL>)
```

**🗓️ 最近更動紀錄：**
在表格新增一列：
```
| YYYY-MM-DD | <變更摘要> | ✅ | ✅ | ✅ |
```

**🕳️ 踩坑筆記：**
若有新坑，依分類記錄：
```
- <問題> → <解決方案 / 待解決>
```

### 4. Git commit + push
```powershell
cd "<工作目錄>"
git add <檔案清單（排除敏感檔案）>
git commit -m "<commit message>"
git push origin <branch>
```

衝突處理：
- 若 push 被拒絕（遠端有新 commit）：告知使用者，**不強制 push**
- 建議使用者先 `git pull --rebase` 再重試

### 5. 報告同步狀態
```
收工完成 ✅

| 平台 | 狀態 | 說明 |
|------|------|------|
| Obsidian 工作筆記 | ✅ 已更新 | 上次做到哪 / 最近更動 / 踩坑 |
| GitHub commit | ✅ <short hash> | <message> |
| GitHub push | ✅ / ❌ | <分支> |

若有失敗項目，請手動處理。
```

## 注意事項

- **無實質進度不執行**（只有提問沒改檔案時跳過）
- **不放敏感檔案**（`.env`、`.claude/`、credentials）
- **commit message 要有資訊**（不要只寫「更新」「修改」）
- **push 失敗要明確告知**，不隱藏錯誤

## 排程整合

收工可透過 dispatcher 排程自動執行。
若需在收工時一併執行排程任務，先執行收工 SOP，再呼叫：
```powershell
.omo\dispatcher.ps1 -RunAll
```
