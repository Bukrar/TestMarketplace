---
name: backup-config
description: 備份當前專案的 Claude Code 配置
---

# 備份專案配置

備份當前專案的 CLAUDE.md 和 settings.json 到 `.claude-backup/` 目錄。

## 執行步驟

1. 取得當前專案路徑
2. 建立備份目錄（含時間戳記）
3. 複製配置檔案到備份目錄
4. 顯示備份結果

## 實作

1. **取得當前時間**
   - 格式：YYYYMMDD_HHMMSS
   - 例如：20250114_103045

2. **建立備份目錄**
   - 路徑：`.claude-backup/[時間戳記]/`
   - 例如：`.claude-backup/20250114_103045/`
   - 使用 Bash 工具建立目錄

3. **複製檔案**
   - 如果 `CLAUDE.md` 存在，複製到備份目錄
   - 如果 `.claude/settings.json` 存在，複製到備份目錄
   - 使用 Bash 工具進行複製

4. **顯示結果**
   - 使用繁體中文
   - 列出備份的檔案
   - 顯示備份目錄位置（Windows 路徑格式）

## 注意事項

- 這是 Windows 環境
- 使用 Windows 路徑格式
- 使用 Bash 工具進行檔案操作

## 範例輸出
```
✓ 配置備份完成！

已備份的檔案：
  • CLAUDE.md
  • settings.json

備份位置：
  C:\Users\USER01\MyProject\.claude-backup\20250114_103045\
```