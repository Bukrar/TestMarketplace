---
name: sync-config
description: 同步團隊的 Claude Code 全域配置
---

# 同步團隊全域配置

這個指令會將團隊標準的 CLAUDE.md 和 settings.json 同步到**使用者的全域配置目錄**。

全域配置會套用到所有專案，確保團隊成員使用一致的開發規範。

## 配置位置

- Windows: `C:\Users\[使用者名稱]\.claude\`
- 所有專案都會使用這些設定

## 執行步驟

1. 取得使用者的 Home 目錄
2. 備份現有的全域配置（如果存在）
3. 複製團隊標準配置到全域目錄
4. 顯示安裝結果

## 實作

請執行以下操作：

1. **取得 Plugin 安裝路徑**
   - Plugin 安裝在：`$CLAUDE_PLUGIN_ROOT`
   - 配置檔案位於：`$CLAUDE_PLUGIN_ROOT/configs/`

2. **取得使用者 Home 目錄**
   - Windows 環境變數：`$env:USERPROFILE`
   - 全域配置目錄：`$env:USERPROFILE\.claude`
   - 使用 Bash 工具時：`%USERPROFILE%\.claude`

3. **備份現有的全域配置**（如果存在）
   - 檢查 `%USERPROFILE%\.claude\CLAUDE.md` 是否存在
   - 檢查 `%USERPROFILE%\.claude\settings.json` 是否存在
   - 如果存在，備份到 `%USERPROFILE%\.claude-backup\` 目錄
   - 備份檔名加上時間戳記：例如 `CLAUDE.md.20250114_103045.bak`

4. **建立全域配置目錄**（如果不存在）
   - 使用 Bash 工具建立 `%USERPROFILE%\.claude` 目錄
   - Windows 指令：`mkdir "%USERPROFILE%\.claude"`

5. **複製配置檔案到全域目錄**
   - 複製 `$CLAUDE_PLUGIN_ROOT/configs/CLAUDE.md` 到 `%USERPROFILE%\.claude\CLAUDE.md`
   - 複製 `$CLAUDE_PLUGIN_ROOT/configs/settings.json` 到 `%USERPROFILE%\.claude\settings.json`
   - 使用 Bash 工具進行檔案操作

6. **顯示結果**
   - 使用繁體中文
   - 顯示成功訊息
   - 列出全域配置檔案的完整路徑（Windows 格式）
   - 說明這是全域配置，會套用到所有專案
   - 提醒使用者重啟 IDE

## 注意事項

- **這是全域配置**：會影響所有 Claude Code 專案
- Windows 環境：使用 Windows 路徑格式（C:\）
- 不要使用 Linux 路徑格式（/c/）
- 使用 Bash 工具進行檔案操作

## 錯誤處理

- 如果無法取得 Home 目錄，顯示錯誤訊息
- 如果備份失敗，詢問是否繼續
- 如果複製失敗，顯示詳細錯誤訊息
- 如果權限不足，提示使用者檢查目錄權限

## 範例輸出