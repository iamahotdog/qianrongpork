千容肉品 v44.3｜Sheets API Append 高併發版

為什麼做這版
------------
V44.2：
- backendSuccess = 8/10
- writtenRows = 6/10
- P95 = 7300ms
- Lock timeout = 2

代表把 Sheet 寫入移出 Lock 的方向是對的，
但使用 getLastRow()+1 仍可能讓多個執行競爭同一列。

V44.3 核心改動
--------------
正式訂單與壓力測試都不再使用：

getLastRow()+1
→ setValues()

改成直接呼叫 Google Sheets API：

spreadsheets.values.append

由 Google Sheets API 決定真正的追加位置。

實作方式
--------
使用：
- UrlFetchApp.fetch
- ScriptApp.getOAuthToken()
- Google Sheets API v4 values:append

所以不需要額外啟用 Apps Script Advanced Sheets Service。

正式訂單流程
------------
Lock 外：
- requestId Cache
- 商品主檔讀取
- 表單驗證
- 後端驗價
- requestId reservation

只有商品有啟用庫存管理時：
短 Script Lock
- 最後確認價格 / 上下架 / 上限 / 庫存
- 扣庫存
releaseLock

Lock 外：
- Google Sheets API append 正式訂單
- 確認 updatedRows = 1
- 寫 Cache
- 清 reservation

若庫存已扣但 append 失敗：
- 重新取得 Lock
- 回滾庫存

壓力測試流程
------------
壓力測試 requestId 每筆都是唯一值，因此 V44.3 壓測 handler 不再使用 Script Lock。

流程：
驗證
→ reservation
→ Sheets API append
→ 檢查 updatedRows = 1
→ 回傳 success

這可以真正測出 Google Sheets API append 在近同時寫入時的表現。

版本比較
--------
V44：
5/10 成功
5/10 寫入
P95 9298ms

V44.1：
6/10 成功
6/10 寫入
P95 6905ms

V44.2：
8/10 success
6/10 實際寫入
P95 7300ms
問題：success 與實際落單不一致

V44.3 目標：
- backendSuccess = 10
- writtenRows = 10
- duplicateCount = 0
- failureReasons = {}
- P95 盡量 < 2500ms

部署
----
1. 更新 Apps Script 為 v44.3。
2. 儲存。
3. 管理部署作業 → 編輯既有 Deployment → 新版本 → 部署。
4. 更新 GitHub Pages index.html。
5. health 確認 version: v44.3。

第一次執行時
------------
因為 V44.3 透過 UrlFetchApp 呼叫 Google Sheets API，
Apps Script 可能會要求重新授權。
若跳出授權畫面，請完成授權。

測試
----
先只跑：
stressTest10

只有確認：
backendSuccess = 10
writtenRows = 10

才進：
stressTest30

注意
----
壓力測試仍只寫：
- 壓力測試
- 壓力測試摘要

不寫正式訂單、不扣正式庫存。
