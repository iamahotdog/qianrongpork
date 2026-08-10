千容肉品 v45｜正式穩定版

這是正式營運版本，已移除所有壓力測試相關程式。

保留功能
--------
1. 商品管理表
   - 售價
   - 庫存
   - 是否販售
   - 單次上限
   - 最後更新時間

2. 商品後端驗價
   - 前端價格不可作為正式金額
   - Apps Script 依商品管理表重新計算

3. 庫存管理
   - 庫存空白：不控庫存
   - 庫存 0：售罄
   - 有控庫存時，下單成功自動扣除
   - append 失敗時嘗試回滾庫存

4. 單品訂購上限
   - 預設 20 包
   - 可於商品管理表調整
   - 超過提示私訊官方 LINE

5. requestId 防重複
   - reservation
   - Cache
   - 避免使用者重複送單

6. Google Sheets API values.append
   - 不使用 getLastRow()+1 分配正式訂單列
   - 降低多人同時寫入碰撞

7. HTTP 429 / 5xx 自動重試
   - 指數退避
   - 隨機 jitter
   - 尖峰時以「慢一點，但盡量不漏單」為原則

8. 成功頁零等待
   - 訂單編號立即顯示
   - 複製訂單立即可用
   - 可直接前往 LINE

9. LINE 官方帳號複製
   - @011qisjt
   - 食品保存與配送說明頁可一鍵複製

10. 訂單查詢、熱銷排行、食品保存與配送說明等原功能保留

已移除
------
- 壓力測試工作表程式
- 壓力測試摘要
- stressTest10 / 30 / 50 / 100
- 壓測 Token
- 壓測專用 URL
- 壓測 benchmark / debug helper

實測基準（開發階段紀錄）
----------------------
V44.3 / V44.4 開發壓測結果：
- 10 筆近同時送單：10/10
- 30 筆近同時送單：30/30
- 50 筆近同時送單：50/50
- 100 筆近同時送單會碰 Google Sheets API 每使用者每分鐘寫入配額

因此正式營運可將「50 筆近同時送單零漏單」作為目前已驗證容量基準。

部署
----
1. Apps Script 更新為 v45。
2. 儲存。
3. 管理部署作業 → 編輯既有正式 Deployment → 新版本 → 部署。
4. GitHub Pages 更新 index.html。
5. health check：
   /exec?action=health&callback=test
   應回傳 version: v45。

注意
----
Google Sheets API 必須維持在目前 GCP 專案中啟用。
OAuth / 測試使用者設定也請勿刪除，否則 Apps Script 可能失去 Sheets API 權限。
