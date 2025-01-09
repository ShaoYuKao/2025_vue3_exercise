# 如何使用 Vite CLI 建立 Vue 專案

## 開發環境說明

### 1. 安裝 Node.js

確認你的電腦已安裝 [Node.js](https://nodejs.org/)，建議版本為 LTS 版本（目前通常為 18.x 或以上）。

- 打開終端機 (Terminal)，輸入以下指令檢查版本：
  ```bash
  node -v
  ```  
  確保顯示的版本為 16.x 或以上。

### 2. 安裝 Vite CLI

- 在終端機輸入以下指令安裝 Vite 官方 CLI 工具：
  ```bash
  npm create vite@latest
  ```
  或者使用 Yarn：
  ```bash
  yarn create vite
  ```

### 3. 初始化專案

- 執行指令後，會提示輸入專案名稱。本範例專案名稱 `vue-demo`，例如：
  ```
  Project name: vue-demo
  ```
  （若直接按下 Enter，專案名稱會預設為 `vite-project`。）

- 接著選擇框架，請選擇 **Vue**：
  ```
  ? Select a framework: » - Use arrow-keys. Return to submit.
      Vanilla
  >   Vue
      React
      Preact
      Lit
      Svelte
      Solid
      Qwik
      Angular
      Others
  ```
  使用方向鍵選擇 **Vue** 並按下 Enter。

- 然後選擇語法變體：
  ```
  ? Select a variant: » - Use arrow-keys. Return to submit.
      TypeScript
  >   JavaScript
      Customize with create-vue ↗
      Nuxt ↗
  ```
  可依需求選擇 **Vue**（僅 JavaScript）或 **Vue + TypeScript**。

### 4. 安裝依賴套件

- 導入到專案目錄：(本範例專案名稱 `vue-demo`)
  ```bash
  cd vue-demo
  ```

- 安裝專案依賴：
  ```bash
  npm install
  ```
  或使用 Yarn：
  ```bash
  yarn
  ```

### 5. 啟動開發伺服器

- 啟動 Vite 開發伺服器：
  ```bash
  npm run dev
  ```
  或使用 Yarn：
  ```bash
  yarn dev
  ```

- 伺服器啟動後，終端機會顯示以下訊息：
  ```
  VITE v6.0.7  ready in 626 ms

  ➜  Local:   http://localhost:5173/
  ➜  Network: use --host to expose
  ➜  press h + enter to show help
  ```
  打開瀏覽器，前往 `http://localhost:5173` 檢視專案。

### 6. 開發專案

- 開啟 `src` 資料夾，進行程式碼修改。例如，編輯 `src/App.vue` 來開始開發。

### 7. 建立專案的正式版本 (Production Build)

- 若需打包專案，執行：
  ```bash
  npm run build
  ```
  或使用 Yarn：
  ```bash
  yarn build
  ```
- 打包後的檔案會儲存在 `dist` 資料夾中。

### 8. 預覽打包結果

- 預覽生產版本：
  ```bash
  npm run preview
  ```
  或使用 Yarn：
  ```bash
  yarn preview
  ```

若需要進一步調整 Vite 設定，可修改專案根目錄的 `vite.config.js` 或 `vite.config.ts` 檔案。

## 專案結構說明

基於 **Vue 3** 和 **Vite** 建立的基本專案結構，以下逐一介紹每個目錄與檔案的用途：

```plaintext
|   .gitignore
|   index.html
|   package.json
|   README.md
|   vite.config.js
|   
+---.vscode
|       extensions.json
|       
+---public
|       vite.svg
|       
\---src
    |   App.vue
    |   main.js
    |   style.css
    |   
    +---assets
    |       vue.svg
    |       
    \---components
            HelloWorld.vue
```

### 根目錄

- `.gitignore`
  - 用於指定 Git 應忽略的檔案或目錄，例如 `node_modules`、暫存檔等。

- `index.html`
  - 專案的入口 HTML 檔案，`Vite` 將以此為基礎進行建置與開發服務。
  - 通常用於掛載 Vue 應用程式的根組件 (`App.vue`)。

- `package.json`
  - 包含專案的基本資訊（名稱、版本等）以及套件依賴。
  - 提供執行腳本（如 `dev`、`build`）及相關設定。

- `README.md`
  - 專案的說明文件，通常用來提供專案介紹、安裝指引與使用說明。

- `vite.config.js`
  - Vite 的配置檔案，用於設定開發伺服器、插件或其他自定義行為。

### `.vscode` 資料夾

- `extensions.json`
  - 提供建議的 VS Code 擴充功能，方便開發者在專案中統一開發環境。

### `public` 資料夾

- `vite.svg`
  - 靜態資源存放區，通常放置不需要經 Vite 處理的檔案（如圖像、字型等）。
  - 這裡的資源將被直接複製到最終的建置目錄。

### `src` 資料夾

`src` 是應用的主要目錄，所有程式碼邏輯都在此撰寫。

- `App.vue`
  - Vue 應用程式的根組件，通常作為應用的起始點。
  - 一般會在此載入全域組件或應用程式的主要邏輯。

- `main.js`
  - Vue 應用的入口檔案。
  - 負責建立 Vue 應用，並掛載到 `index.html` 中的 DOM 節點。

  ```javascript
  import { createApp } from 'vue';
  import App from './App.vue';
  import './style.css';

  createApp(App).mount('#app');
  ```

- `style.css`
  - 全域樣式檔案，適用於全域的樣式設定。

### `src/assets` 資料夾

- `vue.svg`
  - 用於存放應用程式的靜態資源，通常是經常使用的圖像或媒體資源。
  - 相較於 `public`，這些資源會經過 Vite 的資產處理（如壓縮或哈希）。

### `src/components` 資料夾

- `HelloWorld.vue`
  - 範例元件，用於展示 Vue 的基礎功能。
  - 可作為初學者的學習素材，也可刪除或替換為自定義的元件。

總結以上說明，這個結構設計簡單、清晰，符合 Vue 3 和 Vite 的最佳實踐：

- **根目錄**處理專案相關配置與說明。
- **`public`** 目錄負責靜態資源。
- **`src`** 包含應用邏輯和樣式。

此架構適合小型專案或原型開發，若要進行更大型專案開發，可以擴充目錄結構，例如：

- 新增 `router` 目錄管理路由。
- 新增 `store` 目錄處理狀態管理（如使用 Pinia 或 Vuex）。
- 新增 `views` 和 `utils` 等目錄以進一步組織專案程式碼。
