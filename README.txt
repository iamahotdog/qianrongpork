千容肉品 v44.1｜縮短 Lock 高併發優化

本次目的
--------
針對 V44 baseline：
- 10 筆近同時送單
- backendSuccess：5/10
- writtenRows：5
- P50：5078ms
- P95：9298ms
- batchElapsed：13874ms

主要問題是 Script Lock 區段過大。

V44.1 正式訂單優化
------------------
Lock 外先完成：
1. requestId Cache 檢查
2. 商品主檔 Cache 讀取
3. 表單格式驗證
4. 商品驗證
5. 初步驗價

Lock 內只保留：
1. 防重複 Sheet 最終檢查
2. 只讀本次商品的最新 B:E 設定
3. 最後確認售價 / 上下架 / 單次上限 / 庫存
4. 只扣本次有控庫存的商品
5. 寫入正式訂單

寫完立即 releaseLock。

Lock 外再做：
1. 訂單 Cache
2. 熱銷 Cache 失效
3. 商品 Catalog Cache 失效

重要：
正式訂單仍以 Lock 當下的商品管理表最新售價及庫存為準，
沒有為了速度犧牲後端驗價與庫存正確性。

V44.1 壓力測試優化
------------------
舊 V44 壓測在 Lock 內：
- 讀商品管理
- 驗價
- 掃壓力測試表找 requestId
- 寫入

V44.1 改成：
Lock 外：
- 商品讀取
- 驗證
- 驗價
- requestId Cache

Lock 內：
- 只寫一列「壓力測試」

因此可以直接比較 Lock 區段縮短後的改善幅度。

另外新增 failureReasons：
若仍有失敗，執行記錄會直接告訴你是：
- Lock timeout
- 商品設定問題
- response 問題
等，不需要再猜。

部署與測試順序
--------------
1. 更新 Apps Script 為 v44.1。
2. 管理部署作業 → 編輯既有 deployment → 新版本 → 部署。
3. health 確認 version: v44.1。
4. setupStressTestEnvironment 不必重建也可以，已有工作表會沿用。
5. 直接執行 stressTest10。
6. 把新的 JSON 結果與「壓力測試摘要」新一列拿來和 baseline 比較。

先不要跑 30。
只有當新一輪 10 筆達到：
- backendSuccess = 10
- writtenRows = 10
且 P95 明顯下降
再進 stressTest30。

清資料：
clearStressTestData
只清明細，不刪摘要。
