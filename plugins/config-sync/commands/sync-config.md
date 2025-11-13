---
name: sync-config
description: 同步團隊的 Claude Code 全域配置
---

# 同步團隊全域配置

將團隊標準配置同步到使用者的全域 `.claude` 目錄。

**重要：這是 Windows 環境，請使用 Windows 命令和路徑格式。**

## 目標位置

**必須**將檔案放到：
- `C:\Users\[使用者名稱]\.claude\CLAUDE.md`
- `C:\Users\[使用者名稱]\.claude\settings.json`

使用 Windows 環境變數：`%USERPROFILE%\.claude\`

## 執行步驟

請使用 **Bash 工具**執行以下操作（Claude Code 在 Windows 上可以使用 Bash）：

### 步驟 1：取得路徑資訊
```bash
# 取得使用者 Home 目錄（Windows 格式）
USER_HOME=$(cygpath -w "$USERPROFILE" 2>/dev/null || echo "$USERPROFILE")
# 結果應該類似：C:\Users\user01

# 目標目錄（Windows 格式）
TARGET_DIR="$USER_HOME\.claude"
# 結果應該是：C:\Users\user01\.claude

# Plugin 配置來源
SOURCE_DIR="$CLAUDE_PLUGIN_ROOT/configs"
```

### 步驟 2：顯示開始訊息
```
同步團隊全域配置
=================================
目標位置：C:\Users\user01\.claude
```

### 步驟 3：檢查並備份現有配置

使用 Bash 工具檢查檔案：
```bash
# 檢查 .claude 目錄是否存在
if [ -d "$TARGET_DIR" ]; then
  echo "發現現有配置，準備備份..."
  
  # 建立備份目錄
  BACKUP_DIR="$USER_HOME\.claude-backup"
  mkdir -p "$BACKUP_DIR"
  
  # 取得時間戳記
  TIMESTAMP=$(date +%Y%m%d_%H%M%S)
  
  # 備份 CLAUDE.md（如果存在）
  if [ -f "$TARGET_DIR/CLAUDE.md" ]; then
    cp "$TARGET_DIR/CLAUDE.md" "$BACKUP_DIR/CLAUDE.md.$TIMESTAMP.bak"
    echo "  ✓ 已備份 CLAUDE.md"
  fi
  
  # 備份 settings.json（如果存在）
  if [ -f "$TARGET_DIR/settings.json" ]; then
    cp "$TARGET_DIR/settings.json" "$BACKUP_DIR/settings.json.$TIMESTAMP.bak"
    echo "  ✓ 已備份 settings.json"
  fi
fi
```

### 步驟 4：建立目標目錄

**關鍵：確保 .claude 目錄存在**
```bash
# 建立 .claude 目錄（如果不存在）
mkdir -p "$TARGET_DIR"

# 確認目錄已建立
if [ -d "$TARGET_DIR" ]; then
  echo "✓ 目標目錄已準備：$TARGET_DIR"
else
  echo "✗ 錯誤：無法建立目錄"
  exit 1
fi
```

### 步驟 5：複製配置檔案
```bash
# 複製 CLAUDE.md
if [ -f "$SOURCE_DIR/CLAUDE.md" ]; then
  cp "$SOURCE_DIR/CLAUDE.md" "$TARGET_DIR/CLAUDE.md"
  echo "✓ CLAUDE.md 已複製"
else
  echo "✗ 錯誤：找不到來源檔案 CLAUDE.md"
  exit 1
fi

# 複製 settings.json
if [ -f "$SOURCE_DIR/settings.json" ]; then
  cp "$SOURCE_DIR/settings.json" "$TARGET_DIR/settings.json"
  echo "✓ settings.json 已複製"
else
  echo "✗ 錯誤：找不到來源檔案 settings.json"
  exit 1
fi
```

### 步驟 6：驗證並顯示結果
```bash
# 驗證檔案已正確安裝
if [ -f "$TARGET_DIR/CLAUDE.md" ] && [ -f "$TARGET_DIR/settings.json" ]; then
  echo ""
  echo "================================="
  echo "✓ 全域配置同步完成！"
  echo "================================="
  echo ""
  echo "已安裝到全域配置目錄："
  echo "  • CLAUDE.md → $TARGET_DIR\CLAUDE.md"
  echo "  • settings.json → $TARGET_DIR\settings.json"
  echo ""
  
  if [ -d "$BACKUP_DIR" ]; then
    echo "原有配置已備份到："
    echo "  $BACKUP_DIR\"
    echo ""
  fi
  
  echo "ℹ️  這是全域配置，會套用到所有 Claude Code 專案。"
  echo ""
  echo "請重啟您的 IDE 讓配置生效。"
else
  echo "✗ 安裝失敗，請檢查錯誤訊息"
  exit 1
fi
```

## 重要說明

### Windows 環境注意事項

1. **路徑格式**：
   - 顯示給使用者的路徑使用 Windows 格式：`C:\Users\user01\.claude`
   - 內部操作使用 Bash 相容格式（Claude Code 的 Bash 工具會處理）

2. **環境變數**：
   - `$USERPROFILE`：Windows 使用者 Home 目錄
   - `$CLAUDE_PLUGIN_ROOT`：Plugin 安裝目錄

3. **目錄分隔符**：
   - 顯示時使用 `\`（Windows）
   - 操作時 Bash 工具會自動處理

### 為什麼使用 Bash 工具

Claude Code 在 Windows 上內建 Bash 工具，可以執行基本的 Unix 命令，這讓跨平台操作更簡單。即使在 Windows 上，Claude Code 的 Bash 工具也能正確處理 Windows 路徑。

### 除錯資訊

如果執行失敗，請檢查：
1. `$USERPROFILE` 環境變數是否正確
2. 是否有寫入權限到使用者目錄
3. Plugin 來源檔案是否存在

可以用以下指令除錯：
```bash
echo "USERPROFILE: $USERPROFILE"
echo "CLAUDE_PLUGIN_ROOT: $CLAUDE_PLUGIN_ROOT"
echo "TARGET_DIR: $TARGET_DIR"
ls -la "$SOURCE_DIR"
```

## 預期輸出範例
```
同步團隊全域配置
=================================
目標位置：C:\Users\user01\.claude

發現現有配置，準備備份...
  ✓ 已備份 CLAUDE.md
  ✓ 已備份 settings.json
✓ 目標目錄已準備：C:\Users\user01\.claude
✓ CLAUDE.md 已複製
✓ settings.json 已複製

=================================
✓ 全域配置同步完成！
=================================

已安裝到全域配置目錄：
  • CLAUDE.md → C:\Users\user01\.claude\CLAUDE.md
  • settings.json → C:\Users\user01\.claude\settings.json

原有配置已備份到：
  C:\Users\user01\.claude-backup\

ℹ️  這是全域配置，會套用到所有 Claude Code 專案。

請重啟您的 IDE 讓配置生效。
```