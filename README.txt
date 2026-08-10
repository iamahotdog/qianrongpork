千容肉品訂購 Web App v40｜訂單可靠性強化

本版本維持：GitHub Pages + Google Apps Script + Google Sheet。

本次調整：
1. 前端不再把「POST 已送出」視為訂單成功。
2. 每次訂單新增 requestId（請求識別碼），POST 後以前端 JSONP 查詢 confirmOrder，只有 Google Sheet 真正查得到該 requestId 才顯示成功頁。
3. requestId 同時作為防重複下單機制；相同訂單在網路異常後重送會沿用同一 requestId。
4. 商品價格、運費與應付總額改由 Google Apps Script 依固定商品目錄重新計算，前端傳入的金額不再被信任。
5. Apps Script 使用 LockService，避免多人同時寫入時重複建立同一 requestId 訂單。
6. 新訂單編號改為 QR + 14位時間 + 3位亂數；舊有14位訂單編號仍可查詢。
7. Apps Script 自動補上缺少欄位：請求識別碼、訂單狀態、付款狀態、出貨狀態等，不需手動重建既有 Google Sheet。
8. 補齊 queryOrder 後端功能，訂單查詢只回傳商品、金額、配送與狀態，不回傳姓名、電話、地址等個資。
9. 保留既有熱銷排行、LINE、食品說明、記住收件資料與版面。
10. 對寫入 Google Sheet 的使用者文字做試算表公式注入防護。

部署步驟：
A. 先備份目前 Google Sheet 與 Apps Script。
B. 將「Google Apps Script 程式碼_v40.txt」內容完整取代 Apps Script 目前程式碼。
C. Apps Script：部署 > 管理部署作業 > 編輯現有 Web App 部署 > 建立新版本 > 部署。
   - 執行身分：我
   - 存取權：任何人（需維持網站可匿名下單的設定）
D. 若部署後 Web App /exec URL 沒有改變，index.html 不需修改 endpoint。
   若你建立的是全新部署，請把 index.html 的 GOOGLE_SHEET_ENDPOINT 換成新的 /exec URL。
E. 將本資料夾的 index.html 更新到 GitHub Pages。
F. 測試順序：
   1) 下1筆測試訂單，確認成功頁的訂單編號與 Sheet 一致。
   2) 同一筆資料送出時模擬網路不穩後重試，確認 Sheet 不會產生重複 requestId。
   3) 測試訂單查詢：新17位訂單編號及舊14位訂單編號。
   4) 確認熱銷標籤仍正常。

重要：
- v40 仍使用 Google Sheet，適合目前團購規模與學習階段；這不是高交易量電商後端。
- 匯款仍是人工核對，付款狀態需由 Sheet 管理者更新。
- 請不要把銀行敏感憑證、API 密鑰寫進公開 GitHub HTML。
