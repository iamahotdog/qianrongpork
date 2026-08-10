千容肉品 v43｜商品管理表＋LINE 帳號複製

一、商品管理表
Apps Script 會建立一張工作表「商品管理」，欄位為：
商品名稱｜售價｜庫存｜是否販售｜單次上限｜最後更新

初始設定：
- 六項商品售價沿用 200 元。
- 庫存預設空白：代表「不控庫存」。
- 是否販售：TRUE。
- 單次上限：20。

管理方式：
1. 改售價：直接修改「售價」。
2. 控庫存：在「庫存」填入數字。
   - 空白 = 不控庫存。
   - 0 = 售罄。
   - 下單成功後，系統會自動扣庫存。
3. 上下架：
   - TRUE = 販售
   - FALSE = 暫停販售
4. 單次上限：
   - 預設 20 包。
   - 可直接在 Sheet 修改。
5. 商品管理資料會同步至前端：
   - 售價
   - 售罄狀態
   - 是否販售
   - 單次上限

第一次使用：
1. 將 v43 Apps Script 貼入並儲存。
2. 在函式選單執行 initializeProductManagement。
3. Google Sheet 會自動新增「商品管理」工作表。
4. 確認內容後，再部署新版本。
5. 更新 GitHub Pages index.html。

注意：
- 正式下單仍以 Apps Script 讀取「商品管理」表後重新驗價。
- 即使 F12 修改前端價格，Google Sheet 訂單仍使用後端正式價格。
- 有設定庫存時，Apps Script 在訂單成功後自動扣庫存。
- 商品管理 API 有約 30 秒快取；下單時會重新讀最新商品管理資料，因此正式訂單不受快取舊價格影響。

二、LINE 官方帳號複製
食品保存與配送說明頁的：
LINE官方帳號：@011qisjt
右側新增「複製」按鈕。
點擊後直接複製 @011qisjt，按鈕短暫顯示「已複製」。

三、部署
Apps Script：
部署 → 管理部署作業 → 編輯正式 deployment → 新版本 → 部署

GitHub Pages：
更新 index.html

health：
正式 /exec?action=health&callback=test
應回傳 version: v43
