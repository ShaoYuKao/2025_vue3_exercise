# 使用 Vue3 和 Three.js 製作 3D 週期表動畫

本範例結合 **Vue 3** 與 **three.js**，製作了一個互動式的 3D 週期表展示。利用 `@three-ts/orbit-controls` 和 `@tweenjs/tween.js` 等函式庫，實現了動態的視覺效果與使用者互動。

## 安裝相依套件

請先安裝所有必要的相依套件：

```bash
npm install three @three-ts/orbit-controls @tweenjs/tween.js
```

以下為 `package.json` 中的主要相依套件：

- `three`: 3D 函式庫
- `@three-ts/orbit-controls`: 相機控制
- `@tweenjs/tween.js`: 動畫函式庫
- `vue`: 前端框架
- `vite`: 建構工具

## 主要組件說明

`ThreeJsEx05.vue` 組件，負責建立和管理 3D 場景。其主要功能包括：

- **資料管理**：包含相機、場景、渲染器等基本物件。
- **方法**：
  - `init()`: 初始化相機、場景、渲染器及控制器。
  - `createTableTargets()`: 建立表格佈局的目標物件。
  - `createSphereTargets()`, `createHelixTargets()`, `createGridTargets()`: 建立其他不同佈局的目標物件。
  - `transform(targets, duration)`: 進行佈局轉換動畫。
  - `animate()`: 啟動動畫循環與渲染。

## 主程式結構

### 引入必要模組

```js
import * as THREE from 'three';
import { TrackballControls } from 'three/addons/controls/TrackballControls.js';
import { CSS3DRenderer, CSS3DObject } from 'three/addons/renderers/CSS3DRenderer.js';
import * as TWEEN from '@tweenjs/tween.js';
```

這些模組提供了 3D 渲染、控制相機、CSS3D 元素以及動畫。

### 資料定義

```js
data() {
  return {
    dynamicCSS: [], // 動態載入的 CSS
    camera: null, // 相機，它決定了我們從哪個角度觀看場景中的物件。相機的設定會影響到最終渲染出來的圖像，例如視角、遠近等。
    scene: null,  // 場景，它是所有 3D 物件的容器。你可以將各種 3D 物件（例如模型、燈光、特效等）加入到場景中，然後場景會負責管理和渲染這些物件。
    renderer: null, // 渲染器，它負責將場景和相機渲染成最終的 2D 影象。three.js 提供了多種渲染器，WebGLRenderer 是最常用的一種，它使用 WebGL 進行渲染。
    controls: null, // 控制器，它可以控制相機的移動，使我們可以用滑鼠和鍵盤控制場景的觀看角度。
    objects: [],
    targets: { table: [], sphere: [], helix: [], grid: [] },
    table: [
      '48', '이의리', 'Pitcher', 3, 6,
      '62', '정해영', 'Pitcher', 3, 3,
      '11', '김도현', 'Pitcher', 11, 3,
      // ...
      'Og', 'Oganesson', '(294)', 18, 7
    ],
  };
}
```

這裡定義了場景、相機、控制器和目標數據。

### 初始化函式

```js
init() {
  this.settingOnloadStyle();

  // 建立相機
  this.camera = new THREE.PerspectiveCamera(40, window.innerWidth / window.innerHeight, 1, 10000);
  this.camera.position.z = 3000;

  // 建立場景
  this.scene = new THREE.Scene();

  // Add empty placeholders for other transformations
  this.targets.table = this.createTableTargets();
  this.targets.sphere = this.createSphereTargets();
  this.targets.helix = this.createHelixTargets();
  this.targets.grid = this.createGridTargets();

  this.renderer = new CSS3DRenderer();
  this.renderer.setSize(window.innerWidth, window.innerHeight);
  document.getElementById('container').appendChild(this.renderer.domElement);

  // 建立軌跡球控制器
  this.controls = new TrackballControls(this.camera, this.renderer.domElement);

  // 限制縮放範圍
  this.controls.minDistance = 500;
  this.controls.maxDistance = 6000;
  this.controls.addEventListener('change', this.renderScene);

  this.transform(this.targets.table, 2000);
}
```

此函式初始化相機、場景、渲染器及控制器，並建立動畫目標。

### Vue 3 生命週期與 Hooks function

Vue 的實體物件從建立、掛載、更新，到銷毀移除，這一連串的過程，我們將它稱作生命週期。在這個過程中，Vue.js 提供了開發者在這些週期階段做對應處理的 callback function，這些 callback function 我們就稱它叫生命週期的 Hooks function。

| Hooks 名稱 (Vue 2.x/3.x) | Hooks 名稱 (對應 Vue 3.0 Composition API) | 說明                                                               |
|------------------------|---------------------------------------|------------------------------------------------------------------|
| beforeCreate           | setup()                               | Vue 實體被建立，狀態與事件都尚未初始化                                            |
| created                | setup()                               | Vue 實體已建立，狀態與事件已初始化完成 (prop、data、computed 等屬性已建立，vm.$el 屬性無法使用 ) |
| beforeMount            | onBeforeMonut                         | Vue 實體尚未與模板 (DOM 節點) 綁定                                          |
| mounted                | onMounted                             | Vue 實體與掛載完成， el 的目標 DOM 被 $el 所替換 (可以視作 jQuery 的 Ready)          |
| beforeUpdate           | onBeforeUpdate                        | 當狀態被變動時，畫面同步更新前                                                  |
| updated                | onUpdated                             | 當狀態被變動時，畫面已同步更新完成                                                |
| beforeDestroy (2.x)    | onBeforeUnmount                       | Vue 實體物件被銷毀前                                                     |
| beforeUnmount (3.0)    | onBeforeUnmount                       | Vue 實體物件被銷毀前                                                     |
| destroyed (2.x)        | onUnmounted                           | Vue 實體物件被銷毀完畢                                                    |
| unmounted (3.0)        | onUnmounted                           | Vue 實體物件被銷毀完畢                                                    |
| errorCaptured          | onErrorCaptured                       | 子/孫代元件的錯誤被捕獲時觸發                                                  |
| activated              | --                                    | Vue 元件被啟動時觸發，搭配 keep-alive 使用                                    |
| deactivated            | --                                    | Vue 元件被解除時觸發，搭配 keep-alive 使用                                    |

本範例使用 `mounted`, `beforeUnmount` Hooks 的生命週期。

### 動畫轉換函式

```js
transform(targets, duration) {
  TWEEN.removeAll();  // 移除所有的 TWEEN

  for (let i = 0; i < this.objects.length; i++) {
    const object = this.objects[i];
    const target = targets[i];

    // 位置動畫
    new TWEEN.Tween(object.position)
      .to({ x: target.position.x, y: target.position.y, z: target.position.z }, Math.random() * duration + duration)
      .easing(TWEEN.Easing.Exponential.InOut)
      .start();

    // 旋轉動畫
    new TWEEN.Tween(object.rotation)
      .to({ x: target.rotation.x, y: target.rotation.y, z: target.rotation.z }, Math.random() * duration + duration)
      .easing(TWEEN.Easing.Exponential.InOut)
      .start();
  }

  // 觸發渲染
  new TWEEN.Tween(this)
    .to({}, duration * 2)
    .onUpdate(this.renderScene)
    .start();
}
```

這個函式透過 TWEEN.js 將 3D 物件從當前狀態轉換到目標狀態。

## 檢視效果

1. 將 `ThreeJsEx05.vue` 引入至 `App.vue`。
   ```html
   <script setup>
        import ThreeJsEx05 from './components/ThreeJsEx05.vue';
    </script>

    <template>
        <ThreeJsEx05></ThreeJsEx05>
    </template>

    <style scoped>
    </style>
   ```

2. 啟動開發伺服器：
   ```bash
   npm run dev
   ```

3. 打開瀏覽器，即可看到 3D 週期表的展示效果！


## 結語

本範例展示了如何利用 **Vue 3** 與 **three.js** 結合，製作互動性高且具視覺衝擊力的 3D 應用。透過不同的佈局轉換，使用者可以從多角度觀察週期表的結構與元素。

###### 相關文章

- [Three.js – JavaScript 3D Library](https://threejs.org/)
- [tween.js 用户指南](https://tweenjs.github.io/tween.js/docs/user_guide_zh-CN.html)
- [1-7 元件的生命週期與更新機制 | 重新認識 Vue.js | Kuro Hsu](https://book.vue.tw/CH1/1-7-lifecycle.html)
