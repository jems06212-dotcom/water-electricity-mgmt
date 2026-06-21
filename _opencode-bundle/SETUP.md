# 🏠↔🏢 雙地工作設定指南

## 你的實際狀況

| | 家裡（筆電） | 公司（桌電） |
|---|---|---|
| **主要工具** | OpenCode（Sisyphus 調度） | Codex CLI |
| **GDrive 路徑** | `D:\googole driver` | 不同的路徑 |
| **專案檔案在哪** | 在 GDrive 內（自動同步） | 在 `Documents\Codex\` 內（非雲端） |
| **已有進度** | 剛初始化專案 | 已有寫好的文件/企劃 |

## 核心策略：GitHub 當 source of truth

```
GitHub repo (water-electricity-mgmt)
    ↕                          ↕
🏠 家裡 OpenCode  ←───  🏢 公司 Codex
    (git push/pull)     (git push/pull)
```

> GDrive 只輔助同步靜態檔案。**git 才是正式的版本同步工具。**

---

## 第一趟：把公司的 Codex 檔案接進來

你到公司後，照以下步驟把 Codex 寫好的檔案搬進這個專案：

### 在公司電腦上

**① 找出公司的 GDrive 路徑**

```powershell
# 執行看看哪個路徑存在
Test-Path "G:\我的雲端硬碟"
Test-Path "$env:USERPROFILE\Google Drive"
Test-Path "$env:USERPROFILE\My Drive"
```

假設找到的路徑是 `X:\你的GDrive路徑`，下面都用 `<公司GDrive>` 代替。

**② 建立專案資料夾（在 GDrive 內）**

```powershell
mkdir -p "<公司GDrive>\水電管理"
cd "<公司GDrive>\水電管理"
```

**③ 把 Codex 寫好的檔案搬過來**

```powershell
# 把 Codex 工作區的水電管理相關檔案複製過來
Copy-Item -Path "$env:USERPROFILE\Documents\Codex\*水電*" -Destination "." -Recurse
# 或手動把水電管理相關的 .md、.txt、程式檔等搬過來
```

**④ git 初始化 + 接上遠端 repo**

```powershell
git init
git remote add origin https://github.com/jems06212-dotcom/water-electricity-mgmt
git fetch origin
git checkout -b master origin/master
```

> 這會把 GitHub 上已有的專案檔案（CLAUDE.md、.gitignore、_opencode-bundle 等）拉下來，
> 跟你搬進來的 Codex 檔案合併。

**⑤ 把 Codex 寫的檔案加入 git**

```powershell
git add .
git commit -m "feat: 匯入 Codex 撰寫的專案文件"
git push origin master
```

✅ 完成。以後 Codex 就直接在 `<公司GDrive>\水電管理\` 這個目錄工作。

**⑥ 設定 Codex 專案級規則**

專案內已有 `.codex\AGENTS.md`，Codex 進這個目錄後會自動讀取。裡面已經寫好：
- GitHub repo 網址
- 日常 git 流程（開始工作 → pull → 結束工作 → push）
- 注意事項

---

## 第二趟之後：日常同步節奏

### 🏢 在公司用 Codex 工作

```powershell
cd "<公司GDrive>\水電管理"

# 開始
git pull origin master          # 拿家裡的最新進度
codex                            # 打開 Codex 開始工作
# （Codex 會自動讀 .codex/AGENTS.md）

# 結束前（手動或請 Codex 幫忙）
git add .
git commit -m "feat: 清楚的 commit 訊息"
git push origin master
```

### 🏠 回家用 OpenCode 工作

```powershell
cd "D:\googole driver\水電管理"

# 說「開工」→ 我會 git fetch 告訴你遠端有更新 → 建議 pull
# 確認 pull 後接續工作

# 說「收工」→ 自動 commit + push + 更新工作筆記
```

---

## 發生衝突怎麼辦？

如果兩邊都改了同一個檔案：

```
# 在家 pull 時出現 conflict
說「開工」後 → 我發現衝突
→ 告知你哪個檔案衝突
→ 你手動選擇要保留哪個版本
→ 再 commit 一次即可
```

**避免衝突的方法：**
- 兩邊不同時編輯同一個檔案
- 每天結束前一定要 commit + push
- 開始工作前一定要 pull

---

## 如果你的公司 GDrive 路徑跟家裡不同

**所有寫死路徑的設定檔需要調整：**

| 檔案 | 裡面有路徑 | 到公司要改成 |
|------|-----------|------------|
| `.codex\AGENTS.md` | `D:\googole driver\...` | `<公司GDrive>\...` |
| `CLAUDE.md` | `D:\googole driver\...` | `<公司GDrive>\...` |

> 如果公司不裝 OpenCode，只純用 Codex，那只需要調整 `.codex\AGENTS.md` 即可。
> CLAUDE.md 是給 OpenCode 讀的，公司沒 OpenCode 就不影響。

---

## 總結一句話

> **公司 Codex：** 把檔案搬進 GDrive 專案 → 設定 git remote → 以後都在這工作 → push
> **家裡 Sisyphus：** 說「開工」→ pull → 工作 → 說「收工」→ push
