千容肉品 v44.2｜接收與寫入分離

目的
----
V44.1 已將 P95 從 9298ms 降到 6905ms，但 10 筆並發仍只有 6/10 成功，
4 筆失敗原因皆為 Lock timeout。

V44.2 進一步把 Google Sheet 寫入移出 Script Lock。

新架構
------
Lock 外：
- 表單驗證
- 商品主檔讀取
- 後端驗價

極短 Lock：
- 同 requestId reservation（避免同一訂單同時重送）
- 只有「有控庫存」時才做最新庫存確認與扣庫存
- 沒有設定庫存的商品，不做庫存 Lock 工作

立即 releaseLock

Lock 外：
- 寫 Google Sheet 正式訂單
- 寫 Cache
- 清熱銷 Cache / 商品 Cache

壓力測試則更簡化：

Lock 外：
- 驗證、驗價

極短 Lock：
- requestId reservation

releaseLock

Lock 外：
- 寫「壓力測試」工作表

這一版測的是：
「把共享鎖從包住 Google Sheet 寫入，改成只保護真正需要互斥的狀態」後，
同時下單能力能否明顯提升。

安全性
------
1. requestId 仍有短期 reservation 防同時重送。
2. 正式訂單成功後仍寫入正式 Cache。
3. 有控庫存的商品仍會在 Lock 內做最後庫存確認。
4. 若扣庫存後正式訂單寫入失敗，會重新取得 Lock 回滾庫存。
5. 後端價格仍以商品管理表為準。

已知取捨
--------
Google Sheet 的實際資料列寫入現在不再包 Script Lock。
這是為了避免每張訂單因 Sheet I/O 互相排隊。

訂單編號使用高熵隨機尾碼，requestId 亦為 UUID 類型，
因此正常使用下碰撞機率極低；正式防重送主要由 requestId reservation + Cache 處理。

部署
----
1. Apps Script 更新為 v44.2。
2. 管理部署作業 → 編輯既有 deployment → 新版本 → 部署。
3. GitHub Pages index.html 更新為 v44.2。
4. health 確認 version: v44.2。

測試順序
--------
先只跑：
stressTest10

與前兩版比較：
V44：
5/10，P95 9298ms

V44.1：
6/10，P95 6905ms，Lock timeout 4

V44.2 目標：
10/10
writtenRows = 10
duplicateCount = 0
P95 盡量 < 2500ms

只有 10/10 成功後，再跑 stressTest30。
