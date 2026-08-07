千容肉品訂購 Web App v39

本次調整：
1. 料理指南輪播圖片格式由 WebP 改回 PNG。
2. index.html 中的四張料理指南圖片路徑同步改為 PNG：
   - assets/guide-pork-parts.png
   - assets/guide-cooking-overview.png
   - assets/guide-stew-braise.png
   - assets/guide-pan-fry.png
3. 保留 v38 的 Banner preload、fetchpriority=high、eager loading 與 decoding=async 設定。
4. 保留料理指南圖片外部載入與延後載入機制，不再將圖片 Base64 內嵌至 index.html。
5. 商品文字、價格、表單、LINE、Google Sheet、訂單查詢與其他既有功能均未更動。

7. 料理指南圖片檔名改為中文：豬肉部位對應圖.png、料理挑豬肉總覽.png、燉湯滷煮怎麼挑.png、煎炒香煎怎麼挑.png。
