# Vue 3 + Vite

這個模板應該能幫助你開始使用 Vite 開發 Vue 3 專案。該模板使用 Vue 3 的 `<script setup>` 單文件組件 (SFCs)，請查看 [script setup 文件](https://v3.vuejs.org/api/sfc-script-setup.html#sfc-script-setup) 以了解更多資訊。

了解更多關於 Vue 的 IDE 支援，請參閱 [Vue 文件擴展指南](https://vuejs.org/guide/scaling-up/tooling.html#ide-support)。

## 專案結構

```plaintext
|   .copilot-commit-message-instructions.md
|   .gitignore
|   index.html
|   package-lock.json
|   package.json
|   README.md
|   vite.config.js
|   
+---.vscode
|       extensions.json
|       
+---doc
|   +---Library
|   \---Vue
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
```

### 根目錄

- `.copilot-commit-message-instructions.md`
  - 說明 git commit message 如何撰寫符合 Conventional Commits 規範的提交訊息。

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

- `style.css`
  - 全域樣式檔案，適用於全域的樣式設定。

### `src/assets` 資料夾

- `vue.svg`
  - 用於存放應用程式的靜態資源，通常是經常使用的圖像或媒體資源。
  - 相較於 `public`，這些資源會經過 Vite 的資產處理（如壓縮或哈希）。

### `src/components` 資料夾

`components` 是存放各個可以重複使用的 Vue 元件，一個 file 會存放一個獨立元件。

### `src/doc` 資料夾

`doc` 是存放相關說明文件
