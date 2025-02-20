# 使用 Vue3 和 Three.js 打造互動式 3D 場景 - 材質與光源效果教學

> 開發環境
> Node.js 版本：18 以上
> Vue 版本：3.5.13
> Three.js 版本：0.172.0
> `@three-ts/orbit-controls` 版本：1.4.7

在這篇範例中，我們將介紹如何使用 Three.js 透過光源和材質來增加真實感。在 example03 為基礎範例來設定，進一步加入了光源，並使用了更真實的材質類型，讓物體的外觀更具立體感。

## 教學目標

1. 使用 `MeshStandardMaterial` 為物體設置反射效果。
2. 添加方向光和環境光來增強場景的光照效果。
3. 維持互動功能，例如 `OrbitControls`。

## 建立 Three.js 的元件

在 `src/components` 資料夾中新增一個 `ThreeJsEx04.vue` 檔案，並撰寫如下內容：

```html
<template>
  <div ref="threeJsContainer"></div>
</template>

<script>
import * as THREE from 'three';
// import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';
import { OrbitControls } from '@three-ts/orbit-controls';

export default {
  name: "ThreeJsEx04",
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
      const material = new THREE.MeshStandardMaterial( { map:texture } );  // 材質

      sphere = new THREE.Mesh(geometry, material); // 建立網格物件
      scene.add(sphere);  // 將物體加入場景

      // 設定相機的位置，確保相機能看見場景中的物體
      this.camera.position.z = 5;

      // 添加方向光，模擬從一個方向發出的光源
      const light = new THREE.DirectionalLight(0xffffff, 1);  // 顏色、強度
      light.position.set(5, 5, 5);  // 設定光源的位置
      scene.add(light); // 將光源加入場景

      // 添加環境光來補充光源效果
      const ambientLight = new THREE.AmbientLight(0x404040, 0.5);
      scene.add(ambientLight);

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

## 教學步驟

### 1. 初始化基本場景

與範例三相同，我們初始化了基本的 Three.js 場景，包括：

- 使用 `THREE.Scene()` 建立場景。
- 使用 `THREE.PerspectiveCamera` 建立相機，並設定相機位置。
- 使用 `THREE.WebGLRenderer` 建立渲染器，並將渲染結果加到 DOM 上。

### 2. 設置材質

本範例使用了 `THREE.MeshStandardMaterial`，這是一種需要光源渲染的材質類型。搭配圖片，使物體表面具有真實的紋理效果。

```javascript
const textureLoader = new THREE.TextureLoader();
const texture = textureLoader.load('https://threejs.org/examples/textures/crate.gif');
const material = new THREE.MeshStandardMaterial({ map: texture });
```

註：example03 中使用了 `THREE.MeshBasicMaterial` 是不會對光源產生任何反應，因此在場景中添加光源後，物體的外觀不會因光源變化而改變。

### 3. 添加光源

- **方向光 (DirectionalLight)**：模擬從特定方向照射的光。
- **環境光 (AmbientLight)**：提供整體柔和的光源效果。

```javascript
const light = new THREE.DirectionalLight(0xffffff, 1);
light.position.set(5, 5, 5);
scene.add(light);

const ambientLight = new THREE.AmbientLight(0x404040, 0.5);
scene.add(ambientLight);
```

### 4. 更新動畫

透過 `requestAnimationFrame` 不斷更新物體的旋轉動畫，並渲染場景：

```javascript
const animate = () => {
  sphere.rotation.x += 0.01;
  sphere.rotation.y += 0.01;

  requestAnimationFrame(animate);
  this.controls.update();
  this.renderer.render(scene, this.camera);
};
animate();
```

### 5. 處理視窗調整

確保畫布在視窗大小變化時自動調整：

```javascript
onWindowResize() {
  this.camera.aspect = window.innerWidth / window.innerHeight;
  this.camera.updateProjectionMatrix();
  this.renderer.setSize(window.innerWidth, window.innerHeight);
}
```

## 檢視效果

1. 將 `ThreeJsEx04.vue` 引入至 `App.vue`。
   ```html
   <script setup>
        import ThreeJsEx04 from './components/ThreeJsEx04.vue';
    </script>

    <template>
        <ThreeJsEx04></ThreeJsEx04>
    </template>

    <style scoped>
    </style>
   ```

2. 啟動開發伺服器：
   ```bash
   npm run dev
   ```

3. 打開瀏覽器，應該能看到一個可互動的 3D 球體能用滑鼠操作視角，並結合材質與光源效果！

![](./images/錄製_2025_01_08_23_06_42_72.gif)

## 主要學習成果

- 瞭解了 `MeshStandardMaterial` 的使用方式。
- 添加了方向光和環境光來增強真實感。
- 在 Vue 3 中整合 Three.js，打造可互動的 3D 場景。

## 結語

透過此範例，我們可以進一步探索更複雜的材質與光源配置，為場景添加更多真實效果。
