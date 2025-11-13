---
name: sync-config
description: 同步團隊的 Claude Code 配置到當前專案
---

# 同步團隊配置

這個指令會將團隊標準的 CLAUDE.md 和 settings.json 同步到當前專案。

## 執行步驟

1. 檢查當前專案路徑
2. 備份現有配置（如果存在）
3. 複製團隊標準配置到專案
4. 顯示安裝結果

## 實作

請執行以下操作：

1. **取得 Plugin 安裝路徑**
   - Plugin 安裝在：`$CLAUDE_PLUGIN_ROOT`
   - 配置檔案位於：`$CLAUDE_PLUGIN_ROOT/configs/`

2. **取得當前專案路徑**
   - 使用 `pwd` 或 Bash 工具取得當前工作目錄
   - 確保路徑是 Windows 格式（例如：C:\Users\USER01\MyProject）

3. **備份現有配置**（如果存在）
   - 檢查專案根目錄是否有 `CLAUDE.md`
   - 檢查 `.claude/settings.json` 是否存在
   - 如果存在，備份到 `.claude-backup/` 目錄，檔名加上時間戳記
   - 時間戳記格式：YYYYMMDD_HHMMSS

4. **複製配置檔案**
   - 使用 Bash 工具複製 `$CLAUDE_PLUGIN_ROOT/configs/CLAUDE.md` 到專案根目錄
   - 建立 `.claude/` 目錄（如果不存在）
   - 複製 `$CLAUDE_PLUGIN_ROOT/configs/settings.json` 到 `.claude/settings.json`

5. **顯示結果**
   - 顯示成功訊息
   - 使用繁體中文
   - 列出安裝的檔案位置（使用 Windows 路徑格式）
   - 提醒使用者重啟 IDE

## 注意事項

- 這是 Windows 環境，使用 Windows 路徑格式（C:\）
- 不要使用 Linux 路徑格式（/c/）
- 使用 Bash 工具進行檔案操作

## 錯誤處理

- 如果無法取得專案路徑，顯示錯誤訊息
- 如果備份失敗，詢問是否繼續
- 如果複製失敗，顯示詳細錯誤訊息

## 範例輸出
```
✓ 配置同步完成！

已安裝的檔案：
  • CLAUDE.md → C:\Users\USER01\MyProject\CLAUDE.md
  • settings.json → C:\Users\USER01\MyProject\.claude\settings.json

原有配置已備份到：
  C:\Users\USER01\MyProject\.claude-backup\20250114_103045\

請重啟您的 IDE 讓配置生效。
```