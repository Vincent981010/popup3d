# 圖片轉 3D 模型

純前端的圖片轉 3D 工具，可上傳**單張圖片**或**三視圖**（正面／側面／頂面），支援真人、動漫、物品、動物。整個專案只有一個 `index.html`，可直接部署到 GitHub Pages。

## 功能

| 模式 | 輸入 | 方法 | 特色 |
|---|---|---|---|
| 本機・單圖 | 1 張圖 | AI 深度估計（Depth Anything）或輪廓膨脹 → 浮雕網格 | 快速，圖片不離開瀏覽器 |
| 本機・三視圖 | 正面＋側面（＋頂面） | 輪廓雕刻（Visual Hull）體素 | 可從三視圖投影顏色 |
| Hugging Face AI | 1 張圖（或三視圖的正面） | 呼叫 Space（預設 `stabilityai/TripoSR`） | 完整 3D 重建 |

- 自動去背：支援透明 PNG；一般圖片從邊緣色彩洪水填充，容差可調。
- 預覽：可拖曳旋轉、縮放。
- 匯出：GLB（含頂點顏色）、OBJ、STL。

## 部署到 GitHub Pages

1. 建立 repo，把 `index.html` 與本 `README.md` 放在根目錄。
2. 到 **Settings → Pages**。
3. **Source** 選 `Deploy from a branch`，Branch 選 `main`、資料夾選 `/ (root)`，儲存。
4. 等待約 1 分鐘，網址為 `https://<帳號>.github.io/<repo 名稱>/`。

不需要任何建置步驟或 `npm install`，所有函式庫皆由 CDN 載入（three.js、`@huggingface/transformers`、`@gradio/client`），部署與使用時需要能連到 `cdn.jsdelivr.net`。

## 使用方式

### 單張圖片
1. 切換到「單張圖片」，上傳圖片。
2. 選擇深度來源：真人／照片用「AI 深度估計」，動漫／插畫用「輪廓膨脹」。
3. 調整立體強度、背面（鏡像或平面）、網格解析度。
4. 按「生成 3D 模型」。

### 三視圖
1. 切換到「三視圖」，上傳正面與側面圖（頂面圖選填）。
2. 三張圖需為正交視圖，且主體高度一致。
3. 若側面方向不對，切換「側面圖角色朝向」；頂面圖的上方代表角色背後。
4. 調整體素解析度後生成。

### Hugging Face AI
1. 「生成引擎」選擇 Hugging Face AI。
2. 可修改 Space ID；Token 選填，填入自己的 HF Token 可提高免費額度。
3. 三視圖模式下只會使用正面圖。

## 注意事項與限制

- 單圖本機模式是 2.5D 浮雕，背面為推估，不是完整重建。
- 三視圖體素模型有方塊感，凹陷處無法還原。
- AI 深度模型首次使用需下載數十 MB，之後由瀏覽器快取；載入失敗時會自動退回輪廓膨脹。
- 免費 Hugging Face Space 可能休眠，首次喚醒需 1–2 分鐘；匿名使用有 GPU 額度與排隊限制。
- Hugging Face Space 的端點名稱（`/preprocess`、`/generate`）依各 Space 而異。若失敗，打開瀏覽器 Console 會印出該 Space 的 API 資訊，可據此修改 `index.html` 中的 `buildRemote()`。
- Token 僅在瀏覽器輸入，不會儲存；**請勿把 Token 寫進程式碼並推到公開 repo**。
- 請確認上傳圖片的使用權，並遵守所用 Space 與模型的授權條款（TripoSR 為 MIT）。

## 專案結構

```
.
├── index.html   # 全部程式碼（HTML / CSS / JS）
└── README.md
```

## 技術

- [three.js](https://threejs.org/)：預覽與匯出
- [Transformers.js](https://huggingface.co/docs/transformers.js)：瀏覽器端深度估計（`Xenova/depth-anything-small-hf`）
- [@gradio/client](https://www.gradio.app/guides/getting-started-with-the-js-client)：呼叫 Hugging Face Space
- [TripoSR](https://github.com/VAST-AI-Research/TripoSR)：預設的 AI 單圖轉 3D 模型
