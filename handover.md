# 利安貿易網站遷移 — 交接文件

> 最後更新：2026-05-19（漢堡抽屜全站完成）  
> 專案：hihosting → GitHub Pages + Cloudflare  
> 工作區：`C:\Users\deann\Documents\Claude\Projects\網站遷移_lyan\`

---

## 一、專案背景

| 項目 | 內容 |
|---|---|
| 網站類型 | 純靜態 HTML/CSS/JS |
| 舊平台 | hihosting（中華電信）|
| 舊網域 | http://www.lyan.com.tw |
| 新網域 | https://www.lyan.com.tw |
| 目標平台 | GitHub Pages + Cloudflare |
| 產業 | 海乾貨食品進口（鮑魚、干貝、刺參等） |

---

## 二、HTML 頁面遷移進度（12 / 12 頁）✅ 全站完工

| 頁面 | 說明 | 狀態 |
|---|---|---|
| `index.html` | 首頁，CSS ticker 跑馬燈 | ✅ |
| `addressbook.html` | 聯絡資訊，表格樣式B，http→https 已修正 | ✅ |
| `australian-seafoods.html` | 澳洲罐頭，圖片已全數更新 | ✅ |
| `branch.html` | 客戶服務，Formspree 表單已整合 | ✅ |
| `catalog.html` | 商品型錄，JS 動態型錄，本次大幅更新商品資料 | ✅ |
| `dried-seafoods.html` | 乾貨商品 | ✅ |
| `frozen-seafoods.html` | 冷凍商品 | ✅ |
| `introduction.html` | 公司簡介，版本A內文已更新 | ✅ |
| `mexician-seafoods.html` | 墨西哥罐頭，圖片/名稱已更新 | ✅ |
| `others.html` | 其他罐頭 | ✅ |
| `powders.html` | 蒟蒻食材 | ✅ |
| `whatsnew.html` | 最新消息，公告已更新 | ✅ |

---

## 三、已完成事項總覽

### 3-1. 全站基礎重構 ✅
- BIG5 → UTF-8 轉換
- 語意化 HTML5 重寫（`<header>`, `<nav>`, `<main>`, `<footer>`, `<address>`）
- CSS Grid/Flex 版型取代 Table 排版
- RWD（768px / 540px / 380px）
- hihosting 廣告碼、追蹤碼、廢棄腳本全數清除
- 所有絕對路徑改為 `./` 相對路徑

### 3-2. 視覺設計系統 Style B ✅ 全站套用
- Header：米色寬帶雙線金框
- 側邊導覽：金框印章按鈕（hover 深棕底米白字）
- 導覽空白區：淡金菱紋底紋（`::after` 偽元素）
- 區塊標題：米色左棕框標籤（`.section-heading-bar`）

### 3-3. 跑馬燈文字 — 全站 11 頁統一 ✅（catalog.html 已移除跑馬燈）
```
專營進口：鮑魚、干貝、刺參、飯岡屋調味南美貝、花膠魚肚、燕窩、南美貝
```

### 3-4. 產品卡片 Style B ✅
深棕頂欄 + 白底圖片區 + 金色漸層底線：
```css
.card-header  { background: #713917; }
.card-img-wrap { background: #ffffff; }
.card-footer  { height:3px; background:linear-gradient(90deg,#c9a96e,#b8860b,#c9a96e); }
```

### 3-5. catalog.html 商品型錄大改版 ✅

#### 架構
- 左側手風琴導覽 + 右側動態商品卡 + Modal 詳細資訊
- 點父分類「+」→ 展開手風琴 + 右側顯示子分類磚塊
- 點父分類「−」→ 右側返回子分類磚塊
- 點子分類磚塊 → 進入商品列表

#### 商品卡統一大小（重要 CSS）
```css
.card-img-wrap {
  height: 160px;
  display: flex; align-items: center; justify-content: center;
  padding: .5rem;
}
.card-img-wrap img {
  max-width: 100%; max-height: 140px;
  object-fit: contain;
}
.modal-img-wrap { flex-shrink: 0; width: 150px; min-height: 150px; }
.modal-img-wrap img { width: 100%; max-height: 220px; object-fit: contain; }
```

### 3-6. 手機版漢堡抽屜導覽 ✅ 全站 5 頁完成（2026-05）

**問題根本原因（兩個 bug 同時存在）：**
1. CSS `display: none` 放在 `@media` 之後，覆蓋媒體查詢 → 移至 `@media (max-width: 540px)` 之前
2. `overflow-y: auto` 容器攔截 `touchstart`，導致 `click` 不觸發 → 加入 `touch-action: manipulation` + `touchend` 處理器

**關鍵實作（catalog.html）：**
```css
/* 必須在 @media 之前 */
.nav-drawer-toggle,
.nav-drawer-close { display: none; }

@media (max-width: 540px) {
  .nav-drawer-toggle { display: flex; /* ... */ }
  /* ... 抽屜樣式 ... */
}

#site-nav li a {
  touch-action: manipulation;          /* 防止容器攔截 tap */
  -webkit-tap-highlight-color: rgba(113,57,23,.15);
}
```
```js
/* touchend 修正：量測垂直位移，< 10px 視為 tap 直接跳頁 */
var _touchStartY = 0;
drawer.addEventListener('touchstart', function(e) {
  _touchStartY = e.touches[0].clientY;
}, { passive: true });
drawer.addEventListener('touchend', function(e) {
  var link = e.target.closest('a[href]');
  if (!link) return;
  var dy = Math.abs(e.changedTouches[0].clientY - _touchStartY);
  if (dy < 10) { e.preventDefault(); window.location.href = link.getAttribute('href'); }
});
```

✅ **2026-05-19 已移植到 introduction.html、whatsnew.html、branch.html、addressbook.html**

### 3-7. 商品圖片更新 ✅（upload/ 資料夾，2026-05）

| 商品 | 圖片檔名 | 備註 |
|---|---|---|
| 澳洲車輪牌鮑魚罐頭 | `1778213450757.png` | |
| 澳洲海吉妞牌野生鮑魚罐頭 | `1778226575478.png` | 說明文字已更新 |
| 中國海吉妞牌鮑魚罐頭 | `1778213547867.png` | |
| 澳洲金星牌鮑魚罐頭 | `1778212799559.png` | 說明文字已更新 |
| 南非吉品乾鮑 | `1778743616943_rot.png` | 原圖旋轉 90° CCW |
| 黃玉參 | `1778737230001.png` | |
| 鱈魚膠 | `1778230671939.png` | |
| 鱸魚膠 | `1778492715602.png` | |
| 花膠筒 | `1778745000204.png` | |
| 豬婆參 | `1778747231541.png` | |
| 墨西哥參 | `1778747693747.png` | |
| 南美烏參 | `1778747900024.png` | |
| 南美參 | `1778748849049.png` | |
| 鰵魚膠 | `172337.png` | |
| 花膠（分類磚） | `172337.png` | |
| 其他產品（分類磚） | `pa3d7ar4t0.jpg` | |
| 智利樂可思貝 + 南美貝（分類磚） | `DBAAE2-A80978068000_52c294627ac13.jpg` | |
| 秘魯南美貝罐頭 | `1778212644130_crop.png` | numpy 自動裁切去除大面積背景 |
| 燕窩（分類磚）+ 4A燕盞 | `1778834624544.png` | |
| 3A燕盞 | `3A.png` | |
| 2A燕盞 | `2A.png` | |
| 小碎燕 | `小碎燕.png` | |
| 乾燕磚 | `1778836933599.png` | |
| 三角燕盞 | `1778838288037.png` | |
| 野生洞燕 | `1779095616387.png` | |
| 乾螺頭 | `18344.jpg` | |

### 3-8. 燕窩圖片後製 ✅（Python / PIL + OpenCV，2026-05）

**步驟一：浮水印移除**  
7 張燕窩圖片右下角有白色四角星（✦），位置一致（圖片 1196×896，浮水印 bbox x=1112–1168, y=812–868）。  
→ 以雙線性插值從四邊採樣背景色填補，原圖備份為 `.bak`

**步驟二：灰色背景改白色**  
→ OpenCV `floodFill` 從四邊種子點填充（tolerance=22）  
→ 形態學 dilation（7×7, 3 次）清除殘留雜點

**備份說明（upload/ 內）：**
- `*.bak` = 原始（含浮水印，灰色背景）
- `*.bak2` = 中間版（無浮水印，灰色背景；部分存在）
- 現行 `*.png` = 最終版（無浮水印，白色背景）

**注意：** cv2.imread 無法讀取中文路徑，需用 PIL 開檔後轉 numpy array 再給 cv2 處理。

### 3-9. branch.html Formspree 表單整合 ✅
- Endpoint：`https://formspree.io/f/mlgzravg`
- 帳號：`lyan.trade@msa.hinet.net`
- ⚠️ 首次有人送出後需點擊 Formspree 確認信（人工操作，無需改程式碼）

---

## 四、CATALOG 現況（商品完整清單）

### 鮑魚 / 乾鮑魚（6件）
1. 日本皇冠牌吉品乾鮑
2. 日本五島明鮑
3. 日本網鮑
4. 澳洲乾鮑
5. 南非乾鮑
6. 南非吉品乾鮑

### 鮑魚 / 鮑魚罐頭（5件）
1. 墨西哥車輪牌鮑魚罐頭
2. 澳洲車輪牌鮑魚罐頭
3. 澳洲海吉妞牌野生鮑魚罐頭
4. 中國海吉妞牌鮑魚罐頭
5. 澳洲金星牌鮑魚罐頭

### 乾海參 / 日本乾刺參（2件）
1. 日本北海道刺參
2. 日本關西產海參

### 乾海參 / 其他海參（3件）
1. 南美烏參
2. 墨西哥參
3. 豬婆參

### 乾海參 / 冷凍海參（2件）
1. 黃玉參
2. 南美參

### 花膠（4件）
1. 鰵魚膠
2. 鱸魚膠
3. 鱈魚膠
4. 花膠筒

### 燕窩（7件）
1. 野生洞燕
2. 4A燕盞
3. 3A燕盞
4. 2A燕盞
5. 三角燕盞
6. 小碎燕
7. 乾燕磚

### 南美貝 / 南美貝罐頭（2件）
1. 智利樂可思貝罐頭（Locos）
2. 秘魯南美貝罐頭（Locos）

### 南美貝 / 冷凍南美貝（1件）
1. 日本飯岡屋調味貝（大）

### 其他產品（3件）
1. 墨西哥車輪牌螺肉罐頭
2. 日本北寄貝罐頭
3. 乾螺頭

---

## 五、⚠️ 未完成事項

### 5-1. ✅ 手機版漢堡抽屜導覽——已全站完成（2026-05-19）

所有 5 個頁面（`catalog.html` + `introduction.html` + `whatsnew.html` + `branch.html` + `addressbook.html`）均已套用漢堡抽屜導覽。

### 5-2. 🟡 LINE 聯絡按鈕

`index.html` 與 `branch.html` 尚未加入 LINE 按鈕，建議加入：
```html
<a href="https://line.me/ti/p/【LINE_ID】" class="line-btn" target="_blank">
  LINE 立即詢價
</a>
```
需先向客戶確認 LINE 帳號 ID。

### 5-3. 🔴 GitHub Pages 部署 + Cloudflare DNS

1. 在 GitHub 建立新 repo（例如 `lyan-trade-website`）
2. 將 `網站遷移_lyan/` 全部內容推送至 `main` branch
3. Repo Settings → Pages → Deploy from branch: main / root
4. 至 Cloudflare 將 `www.lyan.com.tw` CNAME 指向 `<username>.github.io`
5. 啟用 Cloudflare Proxy（橘色雲端）+ Full SSL
6. GitHub Pages Custom Domain 填入 `www.lyan.com.tw`

---

## 六、設計系統快速參考

### 色票
```css
--color-primary:    #713917;  /* 深棕主色 */
--color-primary-dk: #4e2610;  /* 深棕標題 */
--color-gold:       #b8860b;  /* 金色 */
#c9a96e   /* 細金線 */
#fdf6e8   /* 米色底 */
#e8ddd0   /* 頁面底色 */
#f5e6c8   /* hover 文字色 */
#d5b89a   /* 邊框棕金 */
```

### 跑馬燈（必用 inline-block 寫法）
```css
.ticker-track {
  display: inline-block;
  padding-left: 100%;
  white-space: nowrap;
  animation: ticker-scroll 22s linear infinite;
}
@keyframes ticker-scroll {
  0%   { transform: translateX(0); }
  100% { transform: translateX(-100%); }
}
```

---

## 七、現有檔案結構

```
網站遷移_lyan/
├── *.html（12 頁，全部完成）
├── handover.md
├── css/default.css               原始 CSS（供參考，未使用）
├── image/imge/
│   ├── 1-1.gif / 01.gif          Footer 裝飾（仍使用）
│   └── logo.gif / logo2.gif
└── upload/
    ├── *.bak                     燕窩原圖備份（含浮水印，灰色背景）
    ├── *.bak2                    燕窩中間備份（部分，無浮水印，灰色背景）
    └── *.png / *.jpg             所有商品圖片（現行最終版）
```

---

## 八、下一個對話優先順序

| 優先 | 任務 |
|---|---|
| ✅ 完成 | 其他 4 頁補上手機漢堡抽屜導覽（§5-1）|
| 🔴 高 | GitHub Pages 上傳 + Cloudflare DNS（§5-3）|
| 🟡 中 | 確認 LINE 帳號後加入按鈕（§5-2）|
| 🟡 中 | Formspree 首次驗證（送出測試表單後點確認信）|

### 新對話快速開場（直接貼上）

```
這是利安貿易有限公司網站遷移專案（hihosting → GitHub Pages + Cloudflare）。
工作區：C:\Users\deann\Documents\Claude\Projects\網站遷移_lyan\
請先閱讀 handover.md 了解完整進度。
目前優先任務：將 catalog.html 的手機版漢堡抽屜導覽移植到
introduction.html、whatsnew.html、branch.html、addressbook.html。
```

---

*本文件由 Claude (Cowork) 更新於 2026-05-19（漢堡抽屜移植完成）*
