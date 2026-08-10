千容肉品 v46｜Security Hardening

本版基於 v45 正式穩定版，不改正常顧客操作流程。

新增防護
========

1. Honeypot 機器人欄位
--------------------
結帳頁新增一個正常使用者看不到、無法聚焦的「website」欄位。

正常使用者：
website = 空白 → 正常下單

常見自動填表 Bot：
website 被自動填入 → Apps Script 在任何 Sheet/API 操作前直接拒絕。

此功能不需要 CAPTCHA，不增加正常客人的操作步驟。

2. 訂單 Rate Limit
------------------
Apps Script Web App 無法可靠取得客戶端真實 IP，因此不假裝做 IP 封鎖。

本版以訂購人手機號碼的 SHA-256 雜湊作為節流鍵：
同一電話 10 分鐘內最多 6 次有效送單嘗試。

超過顯示：
「短時間內送出訂單次數較多，請稍候再試；如有大量訂購需求請私訊官方LINE。」

注意：
Cache key 不直接保存手機號碼，只保存截短後的 SHA-256 雜湊。

3. 訂單查詢 Rate Limit
----------------------
同一訂單編號 5 分鐘最多查詢 10 次。

用途：
降低程式自動暴力嘗試「訂單號＋匯款後五碼」的效率。

4. 後端輸入強化
---------------
後端新增：
- 控制字元清除
- 拒絕 < >
- 訂購人姓名最大 30 字
- 收件人姓名最大 30 字
- 地址最大 160 字
- 備註最大 300 字

原本既有防護仍保留：
- 手機必須 09 開頭 10 碼
- 匯款後五碼必須 5 碼
- shippingCode 白名單
- 商品只能來自商品管理表
- 數量整數與上限驗證
- 訂單號格式驗證
- requestId 格式驗證
- safeCellText_ 防 Spreadsheet Formula Injection

5. JSONP callback 強化
----------------------
原本已有 callback 字元白名單。
V46 再限制 callback 最長 80 字元。

6. OAuth Scope 明確化
--------------------
整合包根目錄新增：
appsscript_v46.json

建議在 Apps Script：
專案設定 → 顯示 appsscript.json → 將內容更新為提供的 manifest。

V46 只明確要求：
- Google Sheets 讀寫
- UrlFetchApp 外部請求

不加入：
- Gmail
- Google Drive 全域權限
- Calendar
- Contacts

由於正式架構使用 Google Sheets API values.append，
Sheets API 官方支援的寫入 scope 是：
https://www.googleapis.com/auth/spreadsheets

因此此版沒有冒險改用可能不涵蓋 REST Sheets API 的 currentonly scope。

7. 正式管理功能隔離
-------------------
目前公開 doGet 只接受：
- health
- catalog
- hotSales
- confirmOrder
- queryOrder

商品初始化、Sheet migration 等管理函式沒有公開成 Web API action，
只能由 Apps Script 編輯器執行。

部署步驟
========
1. Apps Script 程式碼更新為 v46。
2. 若要啟用明確 OAuth scopes：
   - Apps Script → 專案設定
   - 勾選「在編輯器中顯示 appsscript.json」
   - 將 appsscript_v46.json 內容貼入 appsscript.json
3. 儲存。
4. Google 可能要求重新授權，屬正常現象。
5. 管理部署作業 → 編輯既有正式 Deployment → 新版本 → 部署。
6. GitHub Pages 更新 v46 index.html。
7. health check 應回：
   version:"v46"

建議測試
========
正式部署後做 4 個基本測試：
1. 一般訂單可正常寫入。
2. 訂單查詢正常。
3. 商品管理修改價格後前端可同步。
4. Google Sheet 正式金額仍由後端驗價。

Rate Limit 不需要用正式訂單連續測 7 次。
若要驗證，可在開發副本把 SECURITY_ORDER_RATE_LIMIT 暫時改成 2 測試，
確認後再恢復 6。

限制
====
這是「適合目前 GitHub Pages + Apps Script + Google Sheet 架構」的資安強化，
不是 WAF、Cloudflare Bot Management 或完整身分驗證系統。

由於公開 Web App 無法可靠取得真實 client IP，
Rate Limit 屬於濫用成本提升，不應視為絕對防禦。

未來真的遭遇大量 Bot 灌單時，再考慮：
- Cloudflare Turnstile
- 外部 API Gateway / WAF
- 後端資料庫與伺服器端 session
