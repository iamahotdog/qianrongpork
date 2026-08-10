千容肉品訂購 Web App v42｜Google Sheet 效能優化

本版不改 UI，主要改善成功頁「訂單確認中」等待時間。

後端：
1. 新增 Apps Script CacheService。
   - 訂單寫入後，立即快取正式總額、配送方式、訂單編號與狀態。
   - confirmOrder 正常情況直接讀 Cache，不再掃 Google Sheet。
2. 訂單查詢也改為 Cache 優先。
3. findRowByValue_ 改成單欄 TextFinder：
   - 不再每次讀 N列 × 19欄。
   - 只搜尋「請求識別碼」或「訂單編號」欄，命中後才讀該列。
4. 移除 SpreadsheetApp.flush()。
5. Lock 範圍縮小：
   - 商品驗證／後端驗價在 Lock 外完成。
   - Lock 只保護防重複與單列寫入。
6. 熱銷排行加入 5 分鐘 Cache。
   - 重算時只讀「訂單細項」一欄。
   - 有新訂單時自動清除舊排行 Cache。
7. 保留後端驗價、防竄改價格、requestId 防重複。

前端：
1. 成功頁仍立即顯示。
2. POST 完成後只做：
   - 立即確認一次
   - 若必要，0.5 秒後再確認一次
3. 不再做多段數秒重試。

部署：
1. Apps Script 貼入 v42 程式碼。
2. 部署 → 管理部署作業 → 編輯目前正式部署 → 新版本 → 部署。
3. 不要新增新的 deployment。
4. 再更新 GitHub Pages 的 index.html。

確認：
正式 /exec?action=health&callback=test
應回傳 version: v42

可手動在 Apps Script 執行 testV42Cache_()，
執行記錄應顯示 CacheService = ok。
