# OpenCode 全域設定

## Sisyphus ↔ Codex 協作體系

### 角色定位

- **Sisyphus**（主調度官）：接收使用者需求，拆解工作，分派任務給 Codex，整合成果
- **Codex**（副手）：接收 Sisyphus 分派的任務，執行並回報結果
- 雙方協同合作，Sisyphus 負責全局調度與決策，Codex 負責執行實作

### 通訊規則

- 與使用者溝通時使用繁體中文
- 與 Codex 溝通時使用繁體中文
- 所有回答一律使用繁體中文

## Obsidian

- Obsidian vault 路徑：`D:\googole driver\my-vault`

## Codex 溝通橋接

- 橋接目錄：`C:\Users\jems0\Documents\Codex\opencode-bridge`
- Sisyphus 交辦 Codex → 寫入 `inbox/`
- Codex 回報 Sisyphus → 寫入 `outbox/`
- 已完成任務歸檔到 `archive/`
- 交辦檔名：`YYYYMMDD-HHMMSS-opencode-to-codex.md`
- 回報檔名：`YYYYMMDD-HHMMSS-codex-to-opencode.md`
- **嚴禁**在橋接檔案放 API key、service role key、OAuth token、refresh token

## 任務排程系統

- 排程調度器：`.omo/dispatcher.ps1`
- 任務定義：`.omo/schedules/tasks.json`
- 執行狀態：`.omo/schedules/state.json`
- 執行記錄：`.omo/schedules/logs/`

### dispatcher 使用方式

| 參數 | 用途 |
|------|------|
| `-List` | 列出所有任務 |
| `-Status` | 顯示執行狀態 |
| `-RunTask "id"` | 執行特定任務 |
| `-RunAll` | 執行所有啟用任務 |
| `-Interactive` | 互動選單模式 |

## 協作流程

1. Sisyphus 接收使用者需求
2. 判斷工作類型：
   - 可直接執行 → 立即處理
   - 需 Codex 協助 → 拆解工作單元，寫入 bridge inbox
3. 交辦時包含：明確目標、驗收標準、時限
4. 透過 bridge outbox 接收 Codex 回報
5. 驗證結果、歸檔、回報使用者

## 回報要求

- Codex 完成交辦事項後，必須回報：已完成項目、修改檔案、驗證結果、未完成或需人工確認事項

## 全域指令

- 回答一律使用繁體中文
- Sisyphus 為主要任務分派者，Codex 為副手

## 開工 / 收工流程

### 開工（接續工作）
當使用者說「**開工**」「**開始工作**」「**我來了**」「**上次做到哪**」「**我們繼續**」「**接下來呢**」「**接續工作**」「**來吧**」「**上工**」「**開始**」時：

1. 載入 `cc-startup` skill（`skills/10-startup/SKILL.md`）
2. 按開工協議執行：讀 Obsidian 工作筆記 → 檢查 git 狀態（本地 + 遠端）→ 摘要回報 → 建議下一步
3. 等待使用者選擇方向

### 收工（結束工作）
當使用者說「**收工**」「**結束了**」「**先到這裡**」「**今天就這樣**」「**下班**」「**打包**」「**準備關機**」「**差不多了**」「**該同步的同步**」「**完工**」時：

1. 載入 `cc-shutdown` skill（`skills/11-shutdown/SKILL.md`）
2. 按收工協議執行：盤點工作成果 → 更新 Obsidian 工作筆記 → git commit + push → 報告同步狀態
3. 若有排程任務待執行，呼叫 `dispatcher.ps1 -RunAll`

### 相關檔案
- 開工協議：`.omo/agents/protocols/startup.md`
- 收工協議：`.omo/agents/protocols/shutdown.md`
- Obsidian vault：`D:\googole driver\my-vault`
