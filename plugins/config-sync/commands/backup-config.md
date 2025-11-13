---
name: backup-config
description: 備份當前的 Claude Code 全域配置
---

# 備份全域配置

備份使用者的全域 CLAUDE.md 和 settings.json 到備份目錄。

**這是 Windows 環境，請使用 Windows 路徑格式。**

## 備份位置

備份會存放在：`C:\Users\[使用者名稱]\.claude-backup\`

## 執行步驟

請使用 **Bash 工具**執行以下操作：

### 步驟 1：取得路徑資訊
```bash
# 取得使用者 Home 目錄（Windows 格式）
USER_HOME=$(cygpath -w "$USERPROFILE" 2>/dev/null || echo "$USERPROFILE")

# 全域配置目錄
CONFIG_DIR="$USER_HOME\.claude"

# 備份目錄
BACKUP_DIR="$USER_HOME\.claude-backup"

# 取得時間戳記
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
```

### 步驟 2：顯示開始訊息
```
備份全域配置
=================================
```

### 步驟 3：檢查配置是否存在
```bash
# 檢查 .claude 目錄是否存在
if [ ! -d "$CONFIG_DIR" ]; then
  echo "✗ 找不到全域配置目錄：$CONFIG_DIR"
  echo ""
  echo "提示：請先執行 /sync-config 安裝全域配置"
  exit 1
fi

# 檢查是否有配置檔案
HAS_FILES=false

if [ -f "$CONFIG_DIR/CLAUDE.md" ]; then
  HAS_FILES=true
fi

if [ -f "$CONFIG_DIR/settings.json" ]; then
  HAS_FILES=true
fi

if [ "$HAS_FILES" = false ]; then
  echo "✗ 找不到任何配置檔案"
  echo ""
  echo "提示：請先執行 /sync-config 安裝全域配置"
  exit 1
fi
```

### 步驟 4：建立備份目錄
```bash
# 建立備份目錄（如果不存在）
mkdir -p "$BACKUP_DIR"

# 確認目錄已建立
if [ ! -d "$BACKUP_DIR" ]; then
  echo "✗ 錯誤：無法建立備份目錄"
  exit 1
fi

echo "備份目錄：$BACKUP_DIR"
echo ""
```

### 步驟 5：備份檔案
```bash
BACKUP_COUNT=0

# 備份 CLAUDE.md
if [ -f "$CONFIG_DIR/CLAUDE.md" ]; then
  cp "$CONFIG_DIR/CLAUDE.md" "$BACKUP_DIR/CLAUDE.md.$TIMESTAMP.bak"
  echo "✓ 已備份：CLAUDE.md → CLAUDE.md.$TIMESTAMP.bak"
  BACKUP_COUNT=$((BACKUP_COUNT + 1))
fi

# 備份 settings.json
if [ -f "$CONFIG_DIR/settings.json" ]; then
  cp "$CONFIG_DIR/settings.json" "$BACKUP_DIR/settings.json.$TIMESTAMP.bak"
  echo "✓ 已備份：settings.json → settings.json.$TIMESTAMP.bak"
  BACKUP_COUNT=$((BACKUP_COUNT + 1))
fi
```

### 步驟 6：顯示結果
```bash
if [ $BACKUP_COUNT -gt 0 ]; then
  echo ""
  echo "================================="
  echo "✓ 全域配置備份完成！"
  echo "================================="
  echo ""
  echo "已備份 $BACKUP_COUNT 個檔案到："
  echo "  $BACKUP_DIR\"
  echo ""
  echo "ℹ️  這是全域配置的備份。"
else
  echo ""
  echo "✗ 沒有檔案被備份"
fi
```

## 重要說明

### Windows 環境注意事項

1. **路徑格式**：
   - 顯示給使用者的路徑使用 Windows 格式
   - 備份檔案會加上時間戳記

2. **備份檔名格式**：
   - `CLAUDE.md.20250114_103045.bak`
   - `settings.json.20250114_103045.bak`

### 除錯資訊

如果執行失敗，請檢查：
1. 全域配置目錄是否存在
2. 是否有讀取權限
3. 是否有寫入權限到備份目錄

可以用以下指令除錯：
```bash
echo "CONFIG_DIR: $CONFIG_DIR"
echo "BACKUP_DIR: $BACKUP_DIR"
ls -la "$CONFIG_DIR"
```

## 預期輸出範例
```
備份全域配置
=================================
備份目錄：C:\Users\user01\.claude-backup

✓ 已備份：CLAUDE.md → CLAUDE.md.20250114_103045.bak
✓ 已備份：settings.json → settings.json.20250114_103045.bak

=================================
✓ 全域配置備份完成！
=================================

已備份 2 個檔案到：
  C:\Users\user01\.claude-backup\

ℹ️  這是全域配置的備份。
```