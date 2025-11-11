# 公司 Claude Code Plugins Marketplace

公司內部 Claude Code 插件集合，統一開發環境設定。

## 🚀 快速開始

### 1. 新增 Marketplace
```bash
# 方式 1：使用 GitHub 短網址（推薦）
claude plugin marketplace add your-github-username/your-claude-plugins

# 方式 2：使用完整 Git URL
claude plugin marketplace add https://github.com/your-github-username/your-claude-plugins.git
```

### 2. 查看可用的 Plugins
```bash
claude plugin
```

### 3. 安裝 Plugin
```bash
claude plugin install hello-team
```

### 4. 使用 Plugin
```bash
# 在 Claude Code 中執行
/greet
```

## 📦 可用的 Plugins

### hello-team
- **說明**：團隊問候工具
- **指令**：`/greet`
- **版本**：1.0.0

## 🔄 更新 Plugins
```bash
# 更新 marketplace 資訊
claude plugin marketplace update my-company-tools

# 更新已安裝的 plugins
claude plugin update --all
```

## 👥 團隊成員安裝步驟

1. 確保已安裝 Claude Code
2. 執行上方的「快速開始」步驟
3. 開始使用！

## 📝 專案自動安裝（進階）

在專案根目錄建立 `.claude/settings.json`：
```json
{
  "extraKnownMarketplaces": {
    "my-company-tools": {
      "source": {
        "source": "github",
        "repo": "your-github-username/your-claude-plugins"
      }
    }
  },
  "enabledPlugins": [
    "hello-team"
  ]
}
```

團隊成員 trust 專案資料夾後，plugins 會自動安裝。

## 🛠️ 開發新的 Plugin

請參考 `plugins/hello-team` 的結構建立新的 plugin。

## 📞 聯絡方式

有問題請聯絡：dev@company.com