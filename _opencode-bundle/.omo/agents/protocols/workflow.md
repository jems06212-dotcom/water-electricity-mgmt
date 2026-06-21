# Sisyphus ↔ Codex 協作流程

```
使用者需求
    │
    ▼
Sisyphus（主調度官）
    │
    ├── 判斷：自己能處理？
    │   └── 是 → 直接執行 → 回報
    │
    ├── 判斷：需要 Codex？
    │   └── 是 → 拆解工作單元
    │       │
    │       ├── 寫入 inbox（交辦）
    │       │   - 明確目標
    │       │   - 驗收標準
    │       │   - 時限
    │       │
    │       ├── 通知使用者已交辦
    │       │
    │       ├── Codex 執行（副手）
    │       │   - 讀取 inbox
    │       │   - 執行任務
    │       │   - 寫入 outbox（回報）
    │       │
    │       └── Sisyphus 確認
    │           - 讀取 outbox
    │           - 驗證結果
    │           - 歸檔
    │
    └── 整合回報使用者
```

## 任務分派原則

1. **單一職責**：每個 inbox 檔案只包含一個任務
2. **明確驗收**：交辦時必須寫明「怎樣算完成」
3. **時限標註**：如果任務有時效性，需標註期限
4. **不回傳密鑰**：橋接檔案禁止放任何 credentials
5. **歸檔追蹤**：已完成任務歸檔到 archive，不刪除

## 檔案命名

- 交辦：`YYYYMMDD-HHMMSS-opencode-to-codex.md`
- 回報：`YYYYMMDD-HHMMSS-codex-to-opencode.md`
- 歸檔：保持原檔名，直接移到 archive/
