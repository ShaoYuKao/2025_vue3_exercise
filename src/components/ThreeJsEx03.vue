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
      const texture = textureLoader.load('https://threejs.org/examples/textures/crate.gif'); // 載入貼圖
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
  }
};
</script>

<style scoped>
body {
  margin: 0;
  overflow: hidden;
}
</style>