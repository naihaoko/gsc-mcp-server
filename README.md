# Google Search Console MCP Server

> This project is a fork of [mcp-gsc](https://github.com/AminForou/mcp-gsc) by [Amin Foroutan](https://github.com/AminForou). Special thanks to the original author for creating this amazing tool.

一個將 [Google Search Console](https://search.google.com/search-console/about)（GSC）與 Claude AI Desktop 串接的工具，讓你能夠透過自然語言對話分析你的 SEO 數據。這個整合讓你可以存取資產資訊、關鍵字分析、網址檢查與網站地圖管理——全部都能透過與 Claude 的簡單聊天完成。

---

## 這個工具能為 SEO 專業人士做什麼？

1. **資產管理**  
   - 一次查看所有 GSC 資產
   - 取得驗證細節與基本網站資訊
   - 新增網站到你的帳戶
   - 從帳戶中移除網站

2. **搜尋分析與報告**  
   - 發現哪些搜尋帶來訪客
   - 追蹤曝光次數、點擊數與點擊率
   - 分析一段時間內的表現趨勢
   - 比較不同時期以發現變化
   - **用圖表視覺化你的數據**，由 Claude 產生

3. **網址檢查與索引**  
   - 檢查特定頁面是否有索引問題
   - 查看 Google 上次抓取頁面的時間
   - 一次檢查多個網址
   - 取得可行的建議以改善索引狀況

4. **網站地圖管理**  
   - 查看所有網站地圖及其狀態
   - 直接透過 Claude 提交新網站地圖
   - 檢查網站地圖中的錯誤或警告
   - 監控網站地圖處理狀態

---

## 可用工具

以下是你在完成整合後可以請 Claude 執行的操作：

| **你可以要求什麼**        | **它會做什麼**                                            | **你需要提供什麼**                                 |
|----------------------------|-----------------------------------------------------------|----------------------------------------------------|
| `list_properties`          | 顯示所有 GSC 資產                                         | 無需提供任何資訊                                   |
| `get_site_details`         | 顯示特定網站的詳細資訊                                   | 你的網站網址                                       |
| `add_site`                 | 新增網站到你的 GSC 資產                                   | 你的網站網址                                       |
| `delete_site`              | 從你的 GSC 資產中移除網站                                 | 你的網站網址                                       |
| `get_search_analytics`     | 顯示帶有指標的熱門查詢與頁面                             | 你的網站網址與時間區間                             |
| `get_performance_overview` | 提供網站表現摘要                                         | 你的網站網址與時間區間                             |
| `check_indexing_issues`    | 檢查頁面是否有索引問題                                   | 你的網站網址與要檢查的頁面清單                     |
| `inspect_url_enhanced`     | 詳細檢查特定網址                                         | 你的網站網址與要檢查的頁面                         |
| `get_sitemaps`             | 列出你網站的所有網站地圖                                 | 你的網站網址                                       |
| `submit_sitemap`           | 向 Google 提交新網站地圖                                 | 你的網站網址與網站地圖網址                         |

*如需完整 19 項可用工具及詳細說明，請在設定完成後請 Claude「list tools」。*

---

## 快速開始（不需程式經驗！）

### 1. 設定 Google Search Console API 存取

在使用本工具前，你需要建立 API 憑證，讓 Claude 能存取你的 GSC 數據：

#### 驗證方式

本工具支援兩種驗證方式：

##### 1. OAuth 驗證（推薦）

此方式讓你用自己的 Google 帳號驗證，通常比服務帳號更方便。它將能存取你平常可用的資源。

將 `GSC_SKIP_OAUTH` 設為 "true"、"1" 或 "yes" 可跳過 OAuth 驗證，僅使用服務帳號驗證。

###### 設定步驟：

1. 前往 [Google Cloud Console](https://console.cloud.google.com/) 並建立 Google Cloud 帳號（如尚未擁有）
2. 建立新專案或選擇現有專案
3. [啟用 Search Console API](https://console.cloud.google.com/apis/library/searchconsole.googleapis.com)
4. [新增範圍](https://console.cloud.google.com/auth/scopes) `https://www.googleapis.com/auth/webmasters` 到你的專案
5. 前往[「憑證」頁面](https://console.cloud.google.com/apis/credentials)
6. 點選「建立憑證」並選擇「OAuth 用戶端 ID」
7. 設定 OAuth 同意畫面
8. 應用程式類型選「桌面應用程式」
9. 給你的 OAuth 用戶端命名並點選「建立」
10. 下載 client secrets JSON 檔（檔名類似 `client_secrets.json`）
11. 將此檔案放在腳本同目錄下，或設定 `GSC_OAUTH_CLIENT_SECRETS_FILE` 環境變數指向其位置

首次以 OAuth 驗證執行工具時，會開啟瀏覽器要求你登入 Google 帳號並授權應用程式。授權後，工具會儲存 token 以供日後使用。

##### 2. 服務帳號驗證

此方式使用服務帳號，適合自動化腳本或不想用個人帳號時。需將服務帳號加入 GSC。

###### 設定步驟：

1. 前往 [Google Cloud Console](https://console.cloud.google.com/) 並建立 Google Cloud 帳號（如尚未擁有）
2. 建立新專案或選擇現有專案
3. [啟用 Search Console API](https://console.cloud.google.com/apis/library/searchconsole.googleapis.com)
4. 前往[「憑證」頁面](https://console.cloud.google.com/apis/credentials)
5. 點選「建立憑證」並選擇「服務帳號」
6. 填寫服務帳號細節並點選「建立」
7. 點選新建立的服務帳號
8. 前往「金鑰」分頁並點選「新增金鑰」>「建立新金鑰」
9. 選擇 JSON 格式並點選「建立」
10. 下載金鑰檔並儲存為 `service_account_credentials.json`，放在腳本同目錄下，或設定 `GSC_CREDENTIALS_PATH` 環境變數指向其位置
11. 將服務帳號 email 加入對應的 Search Console 資產

*點擊上方圖片觀看逐步教學影片*

### 2. 安裝所需軟體

你需要在電腦上安裝以下工具：

- [Python](https://www.python.org/downloads/)（3.11 或更新版）- 執行 GSC 與 Claude 連線
- [Node.js](https://nodejs.org/en) - 執行 MCP 檢查器及部分 MCP 元件
- [Claude Desktop](https://claude.ai/download) - 你將與之對話的 AI 助手

請確保 Python 與 Node.js 已正確安裝並加入系統路徑。

### 3. 下載 Google Search Console MCP

你需要將本工具下載到電腦。最簡單方式：

1. 點選本頁上方綠色「Code」按鈕
2. 選擇「Download ZIP」
3. 解壓縮檔案到你容易找到的位置（如 Documents 資料夾）

或如熟悉 Git：

```bash
git clone https://github.com/naihaoko/gsc-mcp-server.git
```

### 4. 安裝必要元件

打開終端機（Mac）或命令提示字元（Windows）：

1. 切換到解壓縮後的資料夾：
   ```bash
   # 範例（請依實際路徑調整）
   cd ~/Documents/gsc-mcp-server
   ```

2. 建立虛擬環境（讓專案依賴獨立）：
   ```bash
   # 使用 uv（推薦）：
   uv venv .venv
   
   # 若未安裝 uv，先安裝：
   pip install uv
   # 再建立虛擬環境：
   uv venv .venv

   # 或用標準 Python：
   python -m venv .venv
   ```

   **注意：** 若安裝 uv 或 pip 時出現「pip not found」錯誤，請見下方說明。

3. 啟動虛擬環境：
   ```bash
   # Mac/Linux：
   source .venv/bin/activate
   
   # Windows：
   .venv\Scripts\activate
   ```

4. 安裝所需依賴：
   ```bash
   # 使用 uv：
   uv pip install -r requirements.txt

   # 或用標準 pip：
   pip install -r requirements.txt
   ```

   **若出現「pip not found」錯誤：**
   ```bash
   # 先確保 pip 已安裝並升級：
   python3 -m ensurepip --upgrade
   python3 -m pip install --upgrade pip
   
   # 再嘗試安裝依賴：
   python3 -m pip install -r requirements.txt
   
   # 或安裝 uv：
   python3 -m pip install uv
   ```

看到命令提示符前有 `(.venv)`，代表虛擬環境已啟動，依賴會安裝在這裡，不會影響系統 Python。

### 5. 連接 Claude 與 Google Search Console

1. 下載並安裝 [Claude Desktop](https://claude.ai/download)（如尚未安裝）
2. 準備好你的 Google 服務帳號憑證檔
3. 打開終端機（Mac）或命令提示字元（Windows），輸入：

```bash
   # Mac 用戶：
   nano ~/Library/Application\ Support/Claude/claude_desktop_config.json
   
   # Windows 用戶：
   notepad %APPDATA%\Claude\claude_desktop_config.json
```

4. 加入以下設定（告訴 Claude 如何連接 GSC）：

#### OAuth 驗證（用你自己的帳號）

   ```json
   {
     "mcpServers": {
       "gscServer": {
         "command": "/FULL/PATH/TO/gsc-mcp-server/.venv/bin/python",
         "args": ["/FULL/PATH/TO/gsc-mcp-server/gsc_server.py"],
         "env": {
           "GSC_OAUTH_CLIENT_SECRETS_FILE": "/FULL/PATH/TO/client_secrets.json"
         }
       }
     }
   }
   ```

#### Service Account驗證

   ```json
   {
     "mcpServers": {
       "gscServer": {
         "command": "/FULL/PATH/TO/-main/.venv/bin/python",
         "args": ["/FULL/PATH/TO/gsc-mcp-server/gsc_server.py"],
         "env": {
           "GSC_CREDENTIALS_PATH": "/FULL/PATH/TO/service_account_credentials.json",
           "GSC_SKIP_OAUTH": "true"
         }
       }
     }
   }
   ```

   **重要：** 請將所有路徑替換為你電腦上的實際位置：
   - 第一個路徑指向虛擬環境內的 Python 執行檔
   - 第二個路徑指向解壓縮資料夾內的 `gsc_server.py`
   - 第三個路徑指向你的 Google 服務帳號憑證 JSON 檔
   
   範例：
   - Mac：
     - Python 路徑：`/Users/yourname/Documents/gsc-mcp-server/.venv/bin/python`
     - 腳本路徑：`/Users/yourname/Documents/gsc-mcp-server/gsc_server.py`
   - Windows：
     - Python 路徑：`C:\\Users\\yourname\\Documents\\gsc-mcp-server\\.venv\\Scripts\\python.exe`
     - 腳本路徑：`C:\\Users\\yourname\\Documents\\gsc-mcp-server\\gsc_server.py`

5. 儲存檔案：
   - Mac：按 Ctrl+O，然後 Enter，再按 Ctrl+X 離開
   - Windows：點選「檔案 > 儲存」，然後關閉 Notepad

6. 重新啟動 Claude Desktop
7. 開啟 Claude 後，應可在工具區看到 GSC 工具

### 6. 開始分析你的 SEO 數據！

現在你可以請 Claude 查詢你的 GSC 數據！Claude 不僅能取得資料，還能分析、解釋趨勢，並產生視覺化圖表協助你理解 SEO 表現。

以下是每個工具的強大範例提示：

| **工具名稱**                   | **範例提示**                                                                                 |
|--------------------------------|---------------------------------------------------------------------------------------------|
| `list_properties`              | 「列出我所有 GSC 資產，並告訴我哪些資產有最多已索引頁面。」                                |
| `get_site_details`             | 「分析 mywebsite.com 的驗證狀態，並解釋所有權細節代表什麼意思。」                          |
| `add_site`                     | 「將我的新網站 https://mywebsite.com 加入 Search Console 並驗證其狀態。」                   |
| `delete_site`                  | 「從 Search Console 移除舊的測試網站 https://test.mywebsite.com。」                        |
| `get_search_analytics`         | 「顯示 mywebsite.com 過去 30 天的前 20 個搜尋查詢，標出 CTR 低於 2% 的，並建議標題改進。」 |
| `get_performance_overview`     | 「為 mywebsite.com 過去 28 天建立表現總覽圖，找出異常下跌或上升，並解釋可能原因。」         |
| `check_indexing_issues`        | 「檢查這些重要頁面是否有索引問題，並依優先順序列出需立即處理的：mywebsite.com/product, mywebsite.com/services, mywebsite.com/about」 |
| `inspect_url_enhanced`         | 「全面檢查 mywebsite.com/landing-page，並給我可行建議以改善其索引狀態。」                  |
| `batch_url_inspection`         | 「檢查我前 5 個產品頁面，找出常見抓取或索引模式，並建議技術 SEO 改進。」                   |
| `get_sitemaps`                 | 「列出 mywebsite.com 的所有網站地圖，找出有錯誤的，並建議後續步驟。」                      |
| `list_sitemaps_enhanced`       | 「分析 mywebsite.com 的所有網站地圖，聚焦錯誤模式，並建立優先處理計畫。」                  |
| `submit_sitemap`               | 「提交我的新產品網站地圖 https://mywebsite.com/product-sitemap.xml，並說明 Google 處理時間。」|
| `get_sitemap_details`          | 「檢查 mywebsite.com/sitemap.xml 的狀態，並解釋警告對 SEO 的意義。」                      |
| `get_search_by_page_query`     | 「哪些搜尋詞帶來流量到我的部落格文章 mywebsite.com/blog/post-title？找出可優化的相關關鍵字。」|
| `compare_search_periods`       | 「比較我網站 1 月與 2 月的表現。哪些查詢成長最多，哪些下滑，可能原因是什麼？」             |
| `get_advanced_search_analytics`| 「分析我的行動裝置搜尋表現，找出曝光高但排名低於 10 的查詢，並建議內容改進以提升排名。」   |

你也可以請 Claude 結合多個工具並分析結果。例如：

- 「找出我前 20 個流量最高的登陸頁，檢查其索引狀態，並建立報告，標出同時有高流量與索引問題的頁面。」
- 「分析我網站過去 90 天的表現趨勢，找出成長最快的查詢，並檢查對應登陸頁是否有技術問題。」
- 「比較桌機與行動裝置搜尋表現，用圖表視覺化差異，並根據表現落差建議需行動優化的頁面。」
- 「找出排名在第 2 頁（第 11-20 名）且曝光高但 CTR 低的查詢，檢查對應網址並建議標題與描述改進。」

Claude 會用 GSC 工具取得資料、易懂呈現、產生視覺化圖表，並根據結果給出可行建議。

---

## 資料視覺化能力

Claude 可協助你用多種方式視覺化 GSC 數據：

- **趨勢圖**：查看指標隨時間變化
- **比較圖**：比較不同時期或維度
- **表現分布圖**：了解內容在各排名的表現
- **相關性分析**：找出不同指標間的關聯
- **熱力圖**：用色彩呈現複雜資料

分析數據時只要請 Claude「視覺化」或「建立圖表」，它會產生合適的圖表協助你理解資訊。

---

## 疑難排解

### 找不到 Python 指令

在 macOS 上，預設 Python 指令通常是 `python3` 而非 `python`，這可能導致部分應用（包含 Node.js 整合）出錯。

若遇到 Python 找不到的錯誤，可建立別名：

1. 建立 Python 別名（只需一次）：
   ```bash
   # macOS 用戶：
   sudo ln -s $(which python3) /usr/local/bin/python
   
   # 若無效，嘗試尋找 Python 安裝路徑：
   sudo ln -s /Library/Frameworks/Python.framework/Versions/3.11/bin/python3 /usr/local/bin/python
   ```

2. 驗證別名是否有效：
   ```bash
   python --version
   ```

這會建立符號連結，讓應用程式呼叫 `python` 時實際使用你的 `python3`。

### Claude 設定問題

若連線有困難：

1. 確認所有設定檔路徑正確且為完整路徑
2. 檢查服務帳號是否有存取 GSC 資產
3. 重新啟動 Claude Desktop
4. 嘗試使用工具時，查看 Claude 回應中的錯誤訊息
5. 手動執行伺服器時，確認虛擬環境已啟動

### 其他異常問題

若安裝或使用過程遇到其他異常：

1. 複製完整錯誤訊息
2. 用 ChatGPT 或 Claude 詳細說明你的問題，包括：
   - 你嘗試做什麼
   - 完整錯誤訊息
   - 作業系統
   - 已嘗試的步驟
3. AI 助手通常能根據你的情境協助診斷與解決技術問題

大多數問題都曾被他人遇過，通常都有簡單解法。

---

## 貢獻

發現 bug 或有改進想法？歡迎在 GitHub 開 issue 或提交 pull request。
