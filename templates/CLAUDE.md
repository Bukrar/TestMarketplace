# Claude Code 全域配置

此檔案包含所有專案共用的 Claude Code 配置規範。

---

## 語言與溝通規範

- **開發語言**：繁體中文為主
- **專案文件、代碼註釋溝通**：以繁體中文為主
- **Claude Code 互動交流**：必須使用繁體中文
  - 即使程式碼註釋為簡體中文，回應仍需轉換為繁體中文
  - 保留專業術語（如 Unity、Lua、GameObject 等）保持英文
  - 絕對不可使用簡體中文回應用戶
- **溝通風格**：專業技術導向，不過度讚美

---

## 開發環境通用設定

**重要提醒：開發環境須知**

- **作業系統**：Windows
- **IDE**：JetBrains Rider / Visual Studio
- **Shell 命令**：Windows CMD/PowerShell
- **重點**：**絕對禁止嘗試 Linux 語法**
  - 不可使用 `/c/Users/` 等 Linux 路徑格式
  - 不可使用 `find` 或 `grep` 等 Linux 命令
  - 必須使用 Windows 專用命令：`dir`、`findstr`、`type` 等
  - 路徑格式必須使用 Windows 格式：`C:\Users\USER01\...`

---

## SubAgent 使用規則

### Python 開發
- **所有** Python 相關任務必須使用 python-expert SubAgent
- 包括但不限於：.py 檔案編輯、Python 除錯、套件管理、程式碼優化
- 遇到 Python 檔案時，不需詢問，直接調用 python-expert

### MCP Server 連接
- Python 相關操作優先使用 python-tools MCP Server
- Server 已提供：python_execute、python_eval、python_analyze、get_system_info、read_file、list_files 等工具

#### **MCP Server 路徑使用規範**

**重要：python-tools MCP Server 運行在 Docker Linux 容器環境**

##### **一、環境架構**
- **執行環境**：Docker Linux 容器 (Python 3.11.13)
- **專案掛載**：支援多專案，使用環境變數控制
  - AG 專案：`C:\Users\USER01\AG\R1Client` → `/app/projects/ag`
  - CM 專案：`C:\Users\USER01\CM` → `/app/projects/cm`
- **工作目錄**：容器內 `/app`
- **環境變數**：`AG_PROJECT_PATH`、`CM_PROJECT_PATH`

##### **二、檔案內路徑規則**

**在 .py 檔案內部**必須使用**容器路徑格式**：

```python
# ✅ 正確 - 檔案內使用容器路徑
# AG 專案
AG_ROOT = "/app/projects/ag"
ag_claude_md = "/app/projects/ag/CLAUDE.md"
ag_assets_dir = "/app/projects/ag/Assets"

# CM 專案
CM_ROOT = "/app/projects/cm"
cm_claude_md = "/app/projects/cm/CLAUDE.md"
cm_server_dir = "/app/projects/cm/server"

# ❌ 錯誤 - 檔案內不可使用 Windows 路徑
PROJECT_ROOT = r"C:\Users\USER01\CM"  # 會找不到路徑
```

**環境自適應方式**（跨環境腳本）：
```python
def get_project_root(project_name):
    """自動偵測專案根路徑

    Args:
        project_name: 'ag' 或 'cm'
    """
    container_paths = {
        'ag': '/app/projects/ag',
        'cm': '/app/projects/cm'
    }
    windows_paths = {
        'ag': r'C:\Users\USER01\AG\R1Client',
        'cm': r'C:\Users\USER01\CM'
    }

    # 優先檢查容器環境
    if os.path.exists(container_paths.get(project_name, '')):
        return container_paths[project_name]
    # 備用 Windows 本地環境
    elif os.path.exists(windows_paths.get(project_name, '')):
        return windows_paths[project_name]
    else:
        raise RuntimeError(f"找不到 {project_name} 專案根目錄")
```

##### **三、外部傳入路徑規則**

**推薦使用 Windows 路徑格式**，由 PathConverter 自動處理：

```python
# ✅ 推薦 - 外部傳入 Windows 路徑
python script.py "C:\Users\USER01\CM\CLAUDE.md"
python script.py "C:\Users\USER01\AG\R1Client\Assets"

# ✅ 也支援 - 直接使用容器路徑
python script.py "/app/projects/cm/CLAUDE.md"
python script.py "/app/projects/ag/Assets"
```

**PathConverter 路徑轉換類**：
```python
class PathConverter:
    # 專案路徑對應表
    PROJECTS = {
        'ag': {
            'windows': r'C:\Users\USER01\AG\R1Client',
            'container': '/app/projects/ag'
        },
        'cm': {
            'windows': r'C:\Users\USER01\CM',
            'container': '/app/projects/cm'
        }
    }

    @classmethod
    def normalize_path(cls, path):
        """自動判斷並轉換路徑"""
        if path.startswith('/app/') or path.startswith('/'):
            return path  # 容器路徑直接使用

        # Windows 路徑轉換
        for project, paths in cls.PROJECTS.items():
            if path.startswith(paths['windows']):
                relative = path[len(paths['windows']):].replace('\\', '/')
                return paths['container'] + relative

        raise ValueError(f"無法識別的專案路徑: {path}")
```

##### **四、路徑對應表範例**

**AG 專案路徑對應**：
| Windows 路徑 | 容器路徑 | 說明 |
|-------------|----------|------|
| `C:\Users\USER01\AG\R1Client` | `/app/projects/ag` | AG 專案根目錄 |
| `C:\Users\USER01\AG\R1Client\Assets` | `/app/projects/ag/Assets` | Unity 資源目錄 |
| `C:\Users\USER01\AG\R1Client\CLAUDE.md` | `/app/projects/ag/CLAUDE.md` | 專案配置檔 |

**CM 專案路徑對應**：
| Windows 路徑 | 容器路徑 | 說明 |
|-------------|----------|------|
| `C:\Users\USER01\CM` | `/app/projects/cm` | CM 專案根目錄 |
| `C:\Users\USER01\CM\client` | `/app/projects/cm/client` | 客戶端目錄 |
| `C:\Users\USER01\CM\server` | `/app/projects/cm/server` | 伺服器端目錄 |
| `C:\Users\USER01\CM\docs` | `/app/projects/cm/docs` | 文件輸出目錄 |

##### **五、使用建議**

1. **內部專用工具**：直接使用容器路徑
   - AG 專案：`/app/projects/ag`
   - CM 專案：`/app/projects/cm`
2. **通用工具腳本**：實作 PathConverter 支援多專案與 Windows 路徑輸入
3. **跨環境腳本**：使用 `get_project_root(project_name)` 環境自適應邏輯
4. **中文路徑**：完全支援，建議使用英文避免潛在問題

---

## Asana Workflow

請依照下列規則處理 Asana Task：
1. **專心思考**
2. **了解任務**，檢查所有story，包含是否有評論、附件。
3.1 **附件**，受限於 MCP 限制，無法直接線上讀取，請直接下載附件到 `.local` 目錄後再讀取
3.2 **Asana 的附件是放在 AWS S3**，download_url 僅有 30min 的時效，為了防止出錯，下載前先請求最新的 download_url
3.3 **Asana MCP 沒有提供 download API**，請從 Asana Task 中取得附件的 download_url，透過 curl -o 命令下載，下載圖片本身，不要下載連結的 html
3.4 **下載附件後，請做比對**，避免下載的檔案是壞檔 or 下載不完全，如果檔案不正常，請刪除後重新下載
3.5 **非常重要提醒**，如果附件是 .png 是圖片格式，不要用讀取程式碼的方式讀取，避免 API 400 錯誤
4. **google driver link**，因為無法線上解讀，請將google driver檔案下載到 `.local\` 目錄後再讀取
4.1 **google driver file type**, 檢查是什麼google file類型，如果是doc，則下載成.docx，如果是試算表，則下載成xlsx
4.2 **read file**, 請使用python mcp server解讀docx or xlsx
5. **評論**，請用asana_get_stories_for_task工具取得所有評論，評論也可能提及
6. **建立To-do List**，開始解析工作，並列出對應的to-do list和我確認，我如果確沒問題，就可以開始往下執行
7. **有完整資料後**，請依 to-do list 內容開始進行工作
8. **任務完成後**，請產生對應的工作文件
9. **產生完文件後**，asana task 不要變成「完成」狀態，請通知我確認結果
10. **我確認完成後**，依照 task 要求的內容，留下相關評論
11. **清除下載到 .local 目錄的附件**，在相關的 task 完成之後，請協助刪除

---
## HTML 文件輸出規範

### 程式碼區塊格式標準

在生成 HTML 格式的技術文檔時，程式碼區塊的格式化需要特別注意以下事項：

#### 問題描述
HTML 預設會將多個空格、換行符號壓縮成單一空格，導致程式碼區塊的縮排和換行無法正確顯示。

#### 解決方案
在 CSS 中為程式碼區塊（`.code-block`）設定以下樣式：

```css
.code-block {
    background: #2d3748;
    color: #48bb78;
    padding: 20px;
    border-radius: 8px;
    overflow-x: auto;
    margin: 15px 0;
    font-family: 'Courier New', monospace;
    line-height: 1.8;           /* 增加行距提升可讀性 */
    white-space: pre;           /* 關鍵設定：保留所有空格和換行 */
}
```

#### 關鍵 CSS 屬性說明

1. **`white-space: pre;`** - 最重要的設定
   - 保留原始文字的所有空格和換行符號
   - 類似 `<pre>` 標籤的效果
   - 確保程式碼的縮排和格式完整呈現

2. **`line-height: 1.8;`** - 提升可讀性
   - 增加行與行之間的間距
   - 讓程式碼更容易閱讀

3. **`overflow-x: auto;`** - 處理過長程式碼
   - 當程式碼行過長時顯示水平滾動條
   - 避免破壞版面配置

4. **`font-family: 'Courier New', monospace;`** - 等寬字體
   - 使用等寬字體確保對齊正確
   - 特別重要於 ASCII 藝術圖或對齊敏感的程式碼

#### HTML 標籤結構

**使用標準 HTML 標籤結構**：
```html
<div class="code-block">
    <pre>程式碼內容</pre>
</div>
```

**注意事項**：
1. 不為程式碼段落添加 HTML 縮排
2. 不要使用 `<code>` 標籤包圍多行程式碼
3. 不要讓 HTML 標記自動調整程式碼縮排
4. 確保特殊字符正確轉義：<, >, & 等

---

## 版本資訊

- **建立日期**：2025-10-09
- **最後更新**：2025-10-09
- **維護者**：Taylor
