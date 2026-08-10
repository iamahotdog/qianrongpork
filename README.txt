千容肉品 v44｜壓力測試環境

目的
----
測試目前「GitHub Pages + Apps Script + Google Sheet」在多人同時下單時的穩定度。

安全設計
--------
1. 測試訂單只寫入「壓力測試」工作表。
2. 不寫入正式訂單表。
3. 不扣除「商品管理」正式庫存。
4. 使用 Script Properties 的隨機 Token 保護測試入口。
5. 測試仍使用正式商品管理表與正式後端驗價邏輯。
6. 測試仍使用 Script Lock，因此可以觀察目前架構在尖峰寫入時的瓶頸。

會自動建立
----------
- 壓力測試
- 壓力測試摘要

第一次使用
----------
1. Apps Script 更新為 v44 並儲存。
2. 管理部署作業 → 編輯原本正式 Deployment → 新版本 → 部署。
3. 確認 health 回傳 version: v44。
4. 在 Apps Script 函式選單執行：
   setupStressTestEnvironment

建議測試順序
------------
請不要一開始就跑 100。

依序執行：
1. stressTest10
2. 查看「壓力測試摘要」
3. stressTest30
4. 查看摘要
5. stressTest50
6. 如果前面都穩定，再跑 stressTest100

摘要欄位
--------
- 目標並發數：這次想同時送出的訂單數。
- HTTP回應數：實際取得的 HTTP response 數。
- 後端成功數：後端回傳 success=true 的數量。
- 寫入筆數：實際進入「壓力測試」Sheet 的數量。
- 重複requestId：理論上應為 0。
- P50：一半請求在此時間內完成。
- P95：95% 請求在此時間內完成，這是主要觀察指標。
- 最大處理時間：最慢的一筆。
- 整批耗時：整個 fetchAll 批次完成時間。

目前判讀規則
------------
- 全部成功且 P95 <= 2500ms：穩定
- 全部成功且 P95 2501~5000ms：可用但需觀察
- 全部成功但 P95 > 5000ms：成功但偏慢
- 寫入筆數或後端成功數不足：有漏單/失敗

測完後
------
可執行：
clearStressTestData

只會清除「壓力測試」明細，
「壓力測試摘要」會保留，方便比較 10／30／50／100 的結果。

注意
----
這個測試是從 Apps Script 使用 UrlFetchApp.fetchAll 對正式 Web App endpoint
進行近似並發請求。它非常適合找出 Apps Script / Lock / Sheet 的瓶頸，
但不等於專業雲端壓測平台的完整網路層模擬。
