千容肉品 v44.4｜429 自動重試最終穩定版

定位
----
這版作為目前 Google Sheet 架構的最後一次可靠性調整。

V44.3 實測：
- 10 人：10/10
- 30 人：30/30
- 50 人：50/50
- 100 人：61/100
- 100 人失敗 39 筆皆為 Google Sheets API HTTP 429 quota exceeded

因此 V44.4 不再重構架構，只處理 API 尖峰配額。

核心改動
--------
Sheets API values.append 若遇到：
- HTTP 429
- HTTP 500
- HTTP 502
- HTTP 503
- HTTP 504

不會立即失敗，而是自動重試。

等待策略：
第 1 次失敗 → 約 1 秒
第 2 次 → 約 2 秒
第 3 次 → 約 4 秒
第 4 次 → 約 8 秒
第 5 次 → 約 15 秒
第 6 次 → 約 15 秒

每次另外加入 0~750ms 隨機 jitter，
避免 30、50、100 個 request 同時醒來後再次一起撞 quota。

最多：
7 次嘗試（初次 + 最多 6 次重試）

設計原則
--------
「慢可以，漏單不可以。」

正常尖峰：
10 / 30 / 50 筆仍走原本快速 append。

只有真正撞到 Sheets API quota 或暫時性 5xx 時，
該筆訂單才會進入等待重試。

使用者前端仍是零等待成功頁，
所以極端尖峰時主要影響：
- 後臺 Sheet 晚一點看到訂單
而不是：
- 客人卡在成功頁等待

壓測新增指標
------------
執行記錄 JSON 會多：
- totalRetryCount：所有 request 總共重試幾次
- requestsWithRetry：有發生重試的 request 數
- maxRetryCount：單一 request 最多重試幾次

例如：
"backendSuccess": 100,
"writtenRows": 100,
"requestsWithRetry": 39,
"totalRetryCount": 78,
"maxRetryCount": 3,
"failureReasons": {}

就表示 100 筆最後全部成功，其中 39 筆曾碰到 quota / 暫時性錯誤。

部署
----
1. Apps Script 更新 v44.4。
2. 儲存。
3. 管理部署作業 → 編輯既有 Deployment → 新版本 → 部署。
4. GitHub Pages index.html 更新 v44.4。
5. health 確認 version: v44.4。

最後壓測
--------
建議只需再跑：
stressTest100

因為 V44.3 已經證明：
10 / 30 / 50 全部零漏單。

如果 100 人仍因 quota 有少數失敗，
也不建議再繼續增加 Google Sheet 架構複雜度。

目前實際團購情境以：
「50 筆近同時送單可 100% 寫入」
已可作為主要容量基準。

之後建議停止可靠性重構，
轉往營運功能：
- 訂單管理
- 庫存警示
- 銷售統計
- 商品排行
- 每日/每團營收
