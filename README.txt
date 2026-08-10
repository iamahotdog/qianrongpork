千容肉品訂購 Web App v41.3｜修正送單 ReferenceError

本次修正：
1. 修正送出訂單時的 JavaScript 錯誤：
   getOrCreatePendingOrderRequest is not defined
2. 移除不存在的舊函式呼叫。
3. requestId 改為只使用既有且正確的：
   getOrCreateRequestId(payload)
4. 修正錯誤提示文字，不再把所有前端程式錯誤誤稱為「網路連線問題」。
5. 保留 v41.2 的所有功能：
   - 收件人電話
   - 收件人同訂購人姓名＋電話同步
   - 後端商品／價格／運費驗證
   - 防重複 requestId
   - Google Sheet 寫入
   - 訂單查詢、熱銷排行、LINE

部署方式：
- 本版只需要更新 GitHub Pages 的 index.html。
- Google Apps Script v41.2 不需修改、不需重新部署。
