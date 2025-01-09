# Three.js 如何將幾何體表面應用圖片

> 開發環境
> Node.js 版本：18 以上
> Vue 版本：3.5.13
> Three.js 版本：0.172.0

在這篇範例中，我們將學習如何使用 Three.js，製作一個具有圖片紋理的幾何體。最終成果是幾何體表面應用圖片的 3D 場景。

## 1. 安裝 Three.js

Three.js 是一個獨立的函式庫，因此需要將其加入專案中。請在 Vue 專案目錄下打開終端機，輸入以下命令：

```bash
npm install three
```

## 2. 建立 Three.js 的元件

在 `src/components` 資料夾中建立一個新的元件，例如 `ThreeJsEx02.vue`，並實作 Three.js 的初始化邏輯。

```html
<template>
  <div ref="threeJsContainer"></div>
</template>

<script>
import * as THREE from 'three';

export default {
  name: "ThreeJsEx02",
  data() {
    return {
      camera: null,
      renderer: null,
    };
  },
  mounted() {
    this.initThreeJs();
    window.addEventListener('resize', this.onWindowResize);
  },
  methods: {
    /**
     * 初始化 Three.js
     */
    initThreeJs() {
      let scene, sphere;

      // 建立場景
      scene = new THREE.Scene();

      // 建立渲染器
      this.renderer = new THREE.WebGLRenderer();
      this.renderer.setSize(window.innerWidth, window.innerHeight); // 場景大小

      // 將渲染器的 DOM 綁到網頁上
      this.$refs.threeJsContainer.appendChild(this.renderer.domElement);

      // 建立相機
      this.camera = new THREE.PerspectiveCamera(
        45, // 視野 (FOV)
        window.innerWidth / window.innerHeight, // 寬高比 (Aspect Ratio)
        0.1,  // 近剪裁面 (Near Clipping Plane)
        100 // 遠剪裁面 (Far Clipping Plane)
      );

      // 建立物體
      const geometry = new THREE.SphereGeometry(1, 32, 32); // 幾何體

      const textureLoader = new THREE.TextureLoader();  // 圖片載入器
			const texture = textureLoader.load('https://threejs.org/examples/textures/crate.gif'); // 載入圖片
			const material = new THREE.MeshBasicMaterial( { map:texture } );  // 材質

      sphere = new THREE.Mesh(geometry, material); // 建立網格物件
      scene.add(sphere);  // 將物體加入場景

      // 設定相機的位置，確保相機能看見場景中的物體
      this.camera.position.z = 5;

      // 建立動畫
      const animate = () => {
        sphere.rotation.x += 0.01; // 讓幾何體繞 X 軸旋轉
        sphere.rotation.y += 0.01; // 讓幾何體定繞 Y 軸旋轉

        // 使用 requestAnimationFrame 進行重繪
        requestAnimationFrame(animate);

        /**
         * 渲染場景和相機。
         * 使用 Three.js 的 renderer 將場景 (scene) 和相機 (camera) 渲染到畫布上。
         */
        this.renderer.render(scene, this.camera);
      };

      animate();
    },
    /**
     * 螢幕寬高改變時的事件
     */
    onWindowResize() {
      // 更新相機的長寬比為視窗的長寬比
      this.camera.aspect = window.innerWidth / window.innerHeight;

      // 並更新投影矩陣
      this.camera.updateProjectionMatrix();

      // 設定渲染器的大小為視窗的寬度和高度
      this.renderer.setSize(window.innerWidth, window.innerHeight);
    }
  },
  /**
   * 組件被銷毀時的事件
   */
  beforeDestroy() {
    window.removeEventListener('resize', this.onWindowResize);
    // ...existing code...
  }
};
</script>

<style scoped>
body {
  margin: 0;
  overflow: hidden;
}
</style>
```

### 步驟解析

以上程式碼大致上與 **example01** 相同，只有 **材質** 部分程式碼有新增。因此此小節只針對不一樣的作解析。

在程式碼片段是用來在 Three.js 中載入圖片並應用到材質上。

```js
const textureLoader = new THREE.TextureLoader();  // 圖片載入器
const texture = textureLoader.load('https://threejs.org/examples/textures/crate.gif'); // 載入圖片
const material = new THREE.MeshBasicMaterial( { map:texture } );  // 材質
```

這段程式碼的作用是：

1. 建立一個 `TextureLoader` 物件，用來載入圖片。
2. 使用 `TextureLoader` 載入一個圖片。
3. 建立一個 `MeshBasicMaterial` 物件，並將載入的圖片應用到這個材質上。

## 3. 將元件加入主頁面

在 `App.vue` 或其他頁面中使用這個元件。

```html
<script setup>
import ThreeJsEx02 from './components/ThreeJsEx02.vue'
</script>

<template>
  <ThreeJsEx02></ThreeJsEx02>
</template>

<style scoped>
</style>
```

## 4. 執行專案

啟動開發伺服器，檢視效果。

```bash
npm run dev
```

打開瀏覽器，應該能看到一個旋轉的球體。

## 完整效果

運行此程式碼後，你將在瀏覽器中看到一個球體，它會持續繞 X 與 Y 軸旋轉，並且視窗大小變化時能動態調整。

![](./images/錄製_2025_01_07_23_04_33_541.gif)
