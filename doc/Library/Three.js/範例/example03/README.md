# 使用 Vue3 和 Three.js 打造互動式 3D 場景 - OrbitControls 教學

> 開發環境
> Node.js 版本：18 以上
> Vue 版本：3.5.13
> Three.js 版本：0.172.0
> `@three-ts/orbit-controls` 版本：1.4.7

在這篇範例中，我們將學習如何使用 Vue 3 與 Three.js，建立一個包含 **OrbitControls** 的 3D 場景。OrbitControls 能讓使用者透過滑鼠進行旋轉、縮放與平移操作，讓場景更具互動性。

## 完整目標

我們將完成以下內容：

1. **建立基本的 3D 場景**：包括場景、相機、渲染器與基本幾何體。
2. **加入 OrbitControls**：讓使用者能操作視角。
3. **動畫更新**：讓 3D 物件有旋轉的動態效果。
4. **響應式設計**：處理螢幕大小變化。

## 步驟解析

### 1. 安裝相關函式庫

安裝 Three.js 相關函式庫

```bash
npm install three @three-ts/orbit-controls
```

### 2. 建立 Three.js 的元件

在 `src/components` 資料夾中新增一個 `ThreeJsEx03.vue` 檔案，並撰寫如下內容：

```html
<template>
  <div ref="threeJsContainer"></div>
</template>

<script>
import * as THREE from 'three';
// import { OrbitControls } from '../../node_modules/three/examples/jsm/controls/OrbitControls.js';
// import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';
import { OrbitControls } from '@three-ts/orbit-controls';

export default {
  name: "ThreeJsEx03",
  data() {
    return {
      camera: null,
      renderer: null,
      controls: null,
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

      // 建立 OrbitControls
      this.controls = new OrbitControls(this.camera, this.renderer.domElement);
      this.controls.update(); // 更新 OrbitControls

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

        // 更新 OrbitControls
        this.controls.update();

        // 渲染場景和相機
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
  beforeUnmount() {
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

以上程式碼大致上與 **example02** 相同，基於 example02，新增了 **OrbitControls** 功能，讓使用者可以透過滑鼠操作相機，提供了更好的互動性。以下是程式碼的重點與分析：

#### 主要差異與改進

1. **引入 OrbitControls**
   - 透過 `OrbitControls` 套件，使用者可以自由旋轉、縮放與移動相機視角。
   - 匯入方式：
     ```javascript
     import { OrbitControls } from '@three-ts/orbit-controls';
     ```
     或從 `three` 函式庫的 `examples` 目錄中引用 `OrbitControls`。這通常是直接從 three 函式庫的安裝目錄中引用的。（範例已註解）：
     ```javascript
     import { OrbitControls } from '../../node_modules/three/examples/jsm/controls/OrbitControls.js';
     ```
     或
     ```javascript
     import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';
     ```
   - 初始化：
     ```javascript
     this.controls = new OrbitControls(this.camera, this.renderer.domElement);
     this.controls.update();
     ```

2. **新增控制更新**
   - 在動畫循環中，新增了 `this.controls.update()`，確保每幀都更新控制器的狀態。

3. **互動性提升**
   - 使用者可以透過滑鼠 **左鍵拖曳** 旋轉視角，**滾輪滾動**縮放，**右鍵拖曳**平移。
   - 提升了 3D 場景的使用體驗。

### 3. 檢視效果

1. 將 `ThreeJsEx03.vue` 引入至 `App.vue`。
   ```html
   <script setup>
        import ThreeJsEx03 from './components/ThreeJsEx03.vue';
    </script>

    <template>
        <ThreeJsEx03></ThreeJsEx03>
    </template>

    <style scoped>
    </style>
   ```

2. 啟動開發伺服器：
   ```bash
   npm run dev
   ```

3. 打開瀏覽器，您應該能看到一個可互動的 3D 球體，並能用滑鼠操作視角！

![](./images/錄製_2025_01_07_23_56_29_851%20(online-video-cutter.com).gif)

## 功能解析

### OrbitControls 功能

- **旋轉視角**：按住滑鼠左鍵拖曳。
- **縮放視角**：滾動滑鼠滾輪。
- **平移視角**：按住滑鼠右鍵拖曳。

### 延伸功能

1. **限制相機移動**：可透過調整 OrbitControls 的參數，限制相機的縮放或旋轉範圍。
   ```javascript
   this.controls.minDistance = 2;
   this.controls.maxDistance = 10;
   this.controls.maxPolarAngle = Math.PI / 2;
   ```

2. **加入更多物體**：嘗試新增其他幾何體或材質，增強場景內容。

3. **性能優化**：啟用 `enableDamping`，增加操作的流暢感。
   ```javascript
   this.controls.enableDamping = true;
   this.controls.dampingFactor = 0.1;
   ```

## 結語

透過這篇教學，學會如何在 Vue 3 中整合 Three.js 並使用 OrbitControls 來提升 3D 場景的互動性。未來您可以基於這個基礎，創建更豐富、動態的 3D 應用程式。
