# 環境同步手冊 — 家裡 ↔ 公司完全一致

> 目標：兩邊的 OpenCode + Codex + MCP + 技能 一模一樣。
> 顏色標記：✅ GDrive/git 自動同步　🔧 手動安裝一次　🔑 手動設定機密

---

## 一、基礎工具（🔧 每台機器各自安裝一次）

| 工具 | 家裡版本 | 安裝指令 |
|------|---------|---------|
| Node.js | v26.3.1 | 去 https://nodejs.org 下載安裝 |
| npm | 11.17.0 | 裝 Node.js 時一起裝 |
| Git | 2.54.0 | `winget install Git.Git` 或官網下載 |
| GitHub CLI | 2.95.0 | `winget install GitHub.cli` 或 `gh` |
| Bun | 1.3.14 | `powershell -c "irm bun.sh/install.ps1 | iex"` |
| Python | 3.14 | 去 python.org 下載安裝（勾 Add to PATH） |

> **版本不必完全一樣**，但建議裝同一個 major 版本避免相容性問題。

---

## 二、npm 全域套件（🔧 每台機器跑一次）

```powershell
npm install -g @openai/codex
npm install -g opencode-ai
npm install -g oh-my-openagent
npm install -g firebase-tools
npm install -g @supabase/mcp-server-supabase
npm install -g @bitbonsai/mcpvault
npm install -g @ast-grep/cli
npm install -g http-server
npm install -g open-computer-use
npm install -g @ramtinj95/opencode-tokenscope
npm install -g @renjfk/opencode-voice
npm install -g speech-opencode
```

> `opencode-ai`、`oh-my-openagent`、`@openai/codex` 這三個最重要，一定要裝。

---

## 三、Python 套件（🔧 每台機器跑一次）

```powershell
pip install notebooklm-mcp-cli
```

> 這是 NotebookLM MCP server 需要的。

---

## 四、Codex MCP Servers（🔧 每台機器設定）

### 4.1 設定檔位置

```
C:\Users\<你的帳號>\.codex\config.toml
```

這個檔案**不能直接複製**，因為路徑會不同。請參考下面的範例，改成你公司的路徑。

### 4.2 config.toml 範本（改路徑後貼上）

```toml
sandbox_mode = "danger-full-access"
approval_policy = "never"
personality = "pragmatic"

notify = [ "C:\\Users\\<你的帳號>\\AppData\\Local\\OpenAI\\Codex\\runtimes\\cua_node\\<版本>\\bin\\node_modules\\@oai\\sky\\bin\\windows\\codex-computer-use.exe", "turn-ended" ]

[features]
js_repl = false
memories = true
workspace_dependencies = true

[mcp_servers.node_repl]
args = []
command = 'C:\Users\<你的帳號>\AppData\Local\OpenAI\Codex\runtimes\cua_node\<版本>\bin\node_repl.exe'
startup_timeout_sec = 120

[mcp_servers.notebooklm]
command = 'C:\Users\<你的帳號>\AppData\Roaming\Python\Python314\Scripts\notebooklm-mcp.exe'

[mcp_servers.obsidian]
command = 'C:\Users\<你的帳號>\AppData\Roaming\npm\mcpvault.cmd'
args = ['<你的GDrive路徑>\my-vault']
startup_timeout_sec = 120

[mcp_servers.firebase]
command = 'npx'
args = ['-y', 'firebase-tools@latest', 'mcp']
startup_timeout_sec = 120

[mcp_servers.supabase]
command = 'powershell'
args = ['-NoProfile', '-ExecutionPolicy', 'Bypass', '-Command', "$url=[Environment]::GetEnvironmentVariable('SUPABASE_URL','User'); $key=[Environment]::GetEnvironmentVariable('SUPABASE_SERVICE_ROLE_KEY','User'); if (-not $url -or -not $key) { throw 'Missing SUPABASE_URL or SUPABASE_SERVICE_ROLE_KEY user environment variable' }; & npx -y '@supabase/mcp-server-supabase@latest' '--supabase-url' $url '--supabase-service-role-key' $key"]
startup_timeout_sec = 120

[mcp_servers.google-forms]
command = 'powershell'
args = ['-NoProfile', '-ExecutionPolicy', 'Bypass', '-Command', "$names=@('GOOGLE_CLIENT_ID','GOOGLE_CLIENT_SECRET','GOOGLE_REFRESH_TOKEN'); foreach ($n in $names) { $v=[Environment]::GetEnvironmentVariable($n,'User'); if (-not $v) { throw ('Missing ' + $n + ' user environment variable') }; Set-Item -Path ('Env:' + $n) -Value $v }; & node '<你放OpenCode的路徑>/google-forms-mcp/build/index.js'"]
startup_timeout_sec = 120

[desktop]
localeOverride = "zh-TW"
integratedTerminalShell = "powershell"
git-always-force-push = true
defaultTerminalLocation = "bottom"
```

> ⚠️ 把 `<你的帳號>`、`<你的GDrive路徑>`、`<你放OpenCode的路徑>` 改成你公司的實際路徑。

### 4.3 google-forms-mcp 自建 MCP（✅ 或 🔧）

這個 MCP 是自建的，source code 在 `D:\OPENCODE_0621\google-forms-mcp`。

**在家裡：** `D:\OPENCODE_0621\google-forms-mcp` 已經 build 好可以直接用。

**到公司：** 兩種方式：
- 把家裡的 `google-forms-mcp` 資料夾**複製到公司相同位置**（不含 node_modules，到公司重裝）
- 或在公司重新 git clone 並 build

```powershell
# 到公司後安裝 dependencies 並 build
cd "<公司OpenCode路徑>\google-forms-mcp"
npm install
npm run build
```

---

## 五、Codex 外掛（🔧 每台機器設定）

Codex 的 plugins 在 `config.toml` 裡啟用，只要 config.toml 有這些設定就會自動裝：

| Plugin | 說明 |
|--------|------|
| documents@openai-primary-runtime | Google Docs |
| spreadsheets@openai-primary-runtime | Google Sheets |
| presentations@openai-primary-runtime | Google Slides |
| github@openai-curated | GitHub |
| computer-use@openai-bundled | 電腦操作 |
| browser@openai-bundled | 瀏覽器 |
| chrome@openai-bundled | Chrome |
| gmail@openai-curated | Gmail |
| slack@openai-curated | Slack |
| google-calendar@openai-curated | Google Calendar |
| linear@openai-curated | Linear |
| google-drive@openai-curated | Google Drive |
| openai-developers@openai-curated | OpenAI API |
| pdf@openai-primary-runtime | PDF |

> 這些都是 Codex 官方 plugin，設定 `enabled = true` 後 Codex 會自動下載。

---

## 六、環境變數（🔑 每台機器手動設定）

這些是 API key / token，**不要寫進任何檔案**，設成 Windows 使用者環境變數：

```powershell
# 公司也要設同樣的值
[Environment]::SetEnvironmentVariable('SUPABASE_URL', '你的值', 'User')
[Environment]::SetEnvironmentVariable('SUPABASE_SERVICE_ROLE_KEY', '你的值', 'User')
[Environment]::SetEnvironmentVariable('GOOGLE_CLIENT_ID', '你的值', 'User')
[Environment]::SetEnvironmentVariable('GOOGLE_CLIENT_SECRET', '你的值', 'User')
[Environment]::SetEnvironmentVariable('GOOGLE_REFRESH_TOKEN', '你的值', 'User')
[Environment]::SetEnvironmentVariable('OPENAI_API_KEY', '你的值', 'User')
```

**設法：** 搜尋 Windows → 「編輯系統環境變數」→ 「環境變數」→ 「使用者變數」→ 新增

或是開 PowerShell 執行上面的指令（值從家裡匯出帶過去）。

---

## 七、OpenCode 設定（✅ GDrive/git 自動同步）

以下檔案已經在 `_opencode-bundle/` 裡，GDrive 會自動同步：

| 檔案 | 位置 | 同步方式 |
|------|------|---------|
| `CLAUDE.md` | `D:\OPENCODE_0621\CLAUDE.md` | ✅ 從 bundle 複製 |
| `.omo/` | `D:\OPENCODE_0621\.omo\` | ✅ 從 bundle 複製 |
| Skills (lazy packs) | `C:\Users\<你>\.agents\skills\` | 🔧 手動安裝一次 |

### Skills 安裝

家裡已有 14 個懶人包技能，到公司後：

```powershell
# 方法一：從 GitHub 直接拉
mkdir "$env:USERPROFILE\.agents\skills\claude-code-lazy-packs" -Force
cd "$env:USERPROFILE\.agents\skills\claude-code-lazy-packs"
git init
git remote add origin https://github.com/mathruffian-dot/claude-code-lazy-packs
git pull origin main

# 方法二：從家裡 GDrive 複製（要先同步）
# 把 C:\Users\jems0\.agents\skills\claude-code-lazy-packs\skills\
# 複製到公司 C:\Users\<公司帳號>\.agents\skills\claude-code-lazy-packs\skills\
```

---

## 八、Codex AGENTS.md（✅ 或 🔧）

### 全域（每台機器）

```
C:\Users\<你的帳號>\.codex\AGENTS.md
```

家裡目前的內容：

```markdown
回答問題時都要用繁體中文。
開專案時先幫我環境檢測...
# 第二大腦（與 OpenCode 同步）
- Obsidian vault 路徑：`<你的GDrive路徑>\my-vault`
# 水電管理專案
- 專案路徑：`<你的GDrive路徑>\水電管理`
...
```

到公司後，把 `<你的GDrive路徑>` 改成公司的 GDrive 路徑。

### 專案級（✅ git 同步）

`水電管理\.codex\AGENTS.md` — 已經在 git repo 裡，兩邊共用。

---

## 九、Codex 授權（🔑 每台機器）

```powershell
gh auth login                     # GitHub 登入
codex login                       # ChatGPT / OpenAI 登入 (Codex auth)
firebase login                    # Firebase 登入（可能需要 --reauth）
```

---

## 十、檢查清單：到公司後一次跑完

### □ 基礎工具
- [ ] Node.js 安裝（node --version）
- [ ] Git 安裝（git --version）
- [ ] GitHub CLI 安裝（gh --version）
- [ ] Python 安裝（python --version）

### □ npm 全域套件
- [ ] npm install -g @openai/codex
- [ ] npm install -g opencode-ai
- [ ] npm install -g oh-my-openagent
- [ ] npm install -g firebase-tools
- [ ] npm install -g @supabase/mcp-server-supabase
- [ ] npm install -g @bitbonsai/mcpvault
- [ ] npm install -g 其他套件（ast-grep, http-server, open-computer-use, opencode-tokenscope, opencode-voice, speech-opencode）

### □ Python 套件
- [ ] pip install notebooklm-mcp-cli

### □ 設定檔
- [ ] 編輯 `%USERPROFILE%\.codex\config.toml`（照範本改路徑）
- [ ] 複製 `_opencode-bundle\CLAUDE.md` 到 `D:\OPENCODE_0621\CLAUDE.md`
- [ ] 複製 `_opencode-bundle\.omo\` 到 `D:\OPENCODE_0621\.omo\`
- [ ] 編輯 `%USERPROFILE%\.codex\AGENTS.md`（改 GDrive 路徑）
- [ ] 安裝懶人包技能

### □ 自建 MCP
- [ ] 複製 google-forms-mcp 原始碼
- [ ] npm install + npm run build

### □ 環境變數
- [ ] SUPABASE_URL
- [ ] SUPABASE_SERVICE_ROLE_KEY
- [ ] GOOGLE_CLIENT_ID
- [ ] GOOGLE_CLIENT_SECRET
- [ ] GOOGLE_REFRESH_TOKEN
- [ ] OPENAI_API_KEY

### □ 授權
- [ ] gh auth login
- [ ] codex login
- [ ] firebase login

### □ 專案接續
- [ ] git clone 水電管理專案（或進現有目錄 git pull）
- [ ] 打開 Codex / OpenCode 說「開工」
