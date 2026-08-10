千容肉品訂購 Web App v41｜後端商品與價格驗證

本版目的：
把「正式訂單金額的決定權」移到 Google Apps Script。
瀏覽器只送商品名稱與數量，不再把商品金額、運費、總金額當作可信任資料傳給後端。

主要調整：
1. 前端送單 payload 精簡為：
   - requestId
   - 訂購人姓名、電話
   - shippingCode
   - 收件人姓名、地址
   - items：商品名稱＋數量
   - 匯款後五碼、備註
2. 前端不再傳：
   - orderNumber
   - shippingMethod 顯示文字
   - totalQty
   - goodsAmount
   - shippingFee
   - totalAmount
3. Apps Script v41 後端商品主檔：
   - 六項商品皆為每包 200 元
   - 可設定 active=true/false 控制是否販售
4. Apps Script 驗證：
   - 商品必須存在且為販售中
   - 每品項數量 1～20 包
   - 單筆訂單總包數上限 60 包
   - 配送方式必須為後端允許值
5. Apps Script 自行計算：
   - 商品總包數
   - 商品金額
   - 滿 3,000 元免運
   - 配送費
   - 應付總額
6. Google Sheet 欄位與既有資料格式不變。
7. 新增 action=catalog，可讀取目前後端商品名稱與正式價格。
8. 新增三個不寫入 Sheet 的 Apps Script 測試函式：
   - testV41Pricing_
   - testV41RejectFakeProduct_
   - testV41FreeShipping_

部署順序：
A. 先更新 Google Apps Script：
   1. 貼入「Google Apps Script 程式碼_v41_後端商品與價格驗證.txt」
   2. 管理部署作業 → 編輯目前使用中的 Web App → 建立新版本
   3. 請編輯現有部署，不要新增另一個部署，這樣 /exec 網址可維持不變

B. 確認部署後，再更新 GitHub Pages：
   - index.html

建議驗收：
1. 老鼠肉×2＋里肌肉×1＋黑貓：
   商品金額 600、運費 290、總額 890
2. 里肌肉×15＋黑貓：
   商品金額 3000、運費 0、總額 3000
3. 在瀏覽器 DevTools 修改前端 price=1 後下單：
   Google Sheet 正式金額仍應由後端以每包 200 元計算。
