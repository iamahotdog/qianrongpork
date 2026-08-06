千容肉品訂購 Web App v38

本次調整：
1. 首頁 Banner 加入預先載入（preload）與高優先權載入（fetchpriority=high）。
2. Banner 設為 eager loading 與非同步解碼，優先顯示首頁主視覺。
3. 將 4 張料理指南圖片從 index.html 的 Base64 內嵌內容移除，改由 assets 資料夾外部載入，明顯縮小 HTML 檔案容量。
4. 料理指南圖片僅在使用者開啟輪播視窗時才載入，並於顯示目前圖片後預先載入下一張。
5. 輪播圖片加入 lazy loading 與非同步解碼。
6. 商品卡文字、商品價格、表單流程、LINE、Google Sheet 與訂單查詢功能均未更動。

請將以下 4 張 WebP 圖片放入網站既有的 assets 資料夾：
- guide-pork-parts.webp
- guide-cooking-overview.webp
- guide-stew-braise.webp
- guide-pan-fry.webp

首頁 Banner 仍使用既有檔案：assets/header.png。
若後續另製作手機版 Banner，可再加入 picture/srcset，不建議在手機端直接下載超大尺寸桌機原圖。
