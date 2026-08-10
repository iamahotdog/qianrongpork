千容肉品訂購 Web App v40.3

本次調整：
1. Google Apps Script endpoint 更新為目前已部署的 v40.2 Web App：
   AKfycbzjcFE_MhK-6H8LjXmw6-ZWwVWPzLl1XTZuZiVxA3WdNAfkPDbnhNoom9pq5P5vI4me
2. 前端後端版本檢查調整為 v40.2。
3. 移除送單後卡住使用者的「確認訂單中…」輪詢流程。
4. POST 送出後立即進入完成頁；後端訂單編號在背景確認完成後自動更新。
5. 背景確認失敗不再跳出「系統目前尚未確認訂單」警告視窗。
6. 新增「收件人同訂購人」勾選功能：
   - 勾選後自動帶入訂購人姓名。
   - 訂購人姓名變更時，收件人姓名同步更新。
   - 勾選期間收件人欄位鎖定，取消勾選後可自行輸入。
7. 若勾選「在此裝置記住我的收件資訊」，會一併記住「收件人同訂購人」設定。
8. 保留 v40.2 後端驗價、防重複 requestId、熱銷排行、訂單查詢、LINE 與既有商品/版面功能。

部署方式：
- 將 index.html 更新至 GitHub Pages 專案。
- Google Apps Script 維持目前已部署的 v40.2，不需要因本版 HTML 再重新部署。
