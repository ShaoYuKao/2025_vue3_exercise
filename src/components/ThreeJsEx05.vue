<template>
  <div id="info">
    <a href="https://threejs.org" target="_blank" rel="noopener">three.js</a> css3d - periodic table.
  </div>
  <div id="container"></div>
  <div id="menu">
    <button @click="transform(targets.table, 2000)">TABLE</button>
    <button @click="transform(targets.sphere, 2000)">SPHERE</button>
    <button @click="transform(targets.helix, 2000)">HELIX</button>
    <button @click="transform(targets.grid, 2000)">GRID</button>
  </div>
</template>

<script>
import * as THREE from 'three';
import { TrackballControls } from 'three/addons/controls/TrackballControls.js';
import { CSS3DRenderer, CSS3DObject } from 'three/addons/renderers/CSS3DRenderer.js';
import * as TWEEN from '@tweenjs/tween.js'

export default {
  name: 'ThreeJsEx05',
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
        '16', '장지수', 'Pitcher', 12, 3,
        '17', '임기영', 'Pitcher', 5, 3,
        '19', '윤중현', 'Pitcher', 6, 3,
        '20', '이준영', 'Pitcher', 7, 3,
        '24', '김정빈', 'Pitcher', 8, 3,
        '26', '한승혁', 'Pitcher', 10, 3,
        '31', '박준표', 'Pitcher', 9, 3,
        '32', '김재열', 'Pitcher', 1, 3,
        '36', '강이준', 'Pitcher', 2, 3,
        '37', 'Sean Patrick Nolin', 'Pitcher', 13, 3,
        '38', '김현준', 'Pitcher', 14, 3,
        '39', '최지민', 'Pitcher', 15, 3,
        '40', '유승철', 'Pitcher', 16, 3,
        '041', '황동하', 'Pitcher', 17, 3,
        '43', '최용준', 'Pitcher', 18, 3,
        '45', 'Thomas Pannone', 'Pitcher', 1, 4,
        '46', '박진태', 'Pitcher', 2, 4,
        '49', '김유신', 'Pitcher', 3, 4,
        '049', '손진규', 'Pitcher', 4, 4,
        '09', '나용기', 'Pitcher', 5, 4,
        '013', '김양수', 'Pitcher', 6, 4,
        '029', '옥준호', 'Pitcher', 7, 4,
        '50', '장현식', 'Pitcher', 8, 4,
        '51', '전상현', 'Pitcher', 9, 4,
        '55', '서덕원', 'Pitcher', 10, 4,
        '56', '장재혁', 'Pitcher', 11, 4,
        '59', '강병우', 'Pitcher', 12, 4,
        '60', '고영창', 'Pitcher', 13, 4,
        '68', '이승재', 'Pitcher', 14, 4,
        'As', 'Arsenic', '74.9216', 15, 4,
        'Se', 'Selenium', '78.96', 16, 4,
        'Br', 'Bromine', '79.904', 17, 4,
        'Kr', 'Krypton', '83.798', 18, 4,
        'Rb', 'Rubidium', '85.4678', 1, 5,
        'Sr', 'Strontium', '87.62', 2, 5,
        'Y', 'Yttrium', '88.90585', 3, 5,
        'Zr', 'Zirconium', '91.224', 4, 5,
        'Nb', 'Niobium', '92.90628', 5, 5,
        'Mo', 'Molybdenum', '95.96', 6, 5,
        'Tc', 'Technetium', '(98)', 7, 5,
        'Ru', 'Ruthenium', '101.07', 8, 5,
        'Rh', 'Rhodium', '102.9055', 9, 5,
        'Pd', 'Palladium', '106.42', 10, 5,
        'Ag', 'Silver', '107.8682', 11, 5,
        'Cd', 'Cadmium', '112.411', 12, 5,
        'In', 'Indium', '114.818', 13, 5,
        'Sn', 'Tin', '118.71', 14, 5,
        'Sb', 'Antimony', '121.76', 15, 5,
        'Te', 'Tellurium', '127.6', 16, 5,
        'I', 'Iodine', '126.90447', 17, 5,
        'Xe', 'Xenon', '131.293', 18, 5,
        'Cs', 'Caesium', '132.9054', 1, 6,
        'Ba', 'Barium', '132.9054', 2, 6,
        'La', 'Lanthanum', '138.90547', 4, 9,
        'Ce', 'Cerium', '140.116', 5, 9,
        'Pr', 'Praseodymium', '140.90765', 6, 9,
        'Nd', 'Neodymium', '144.242', 7, 9,
        'Pm', 'Promethium', '(145)', 8, 9,
        'Sm', 'Samarium', '150.36', 9, 9,
        'Eu', 'Europium', '151.964', 10, 9,
        'Gd', 'Gadolinium', '157.25', 11, 9,
        'Tb', 'Terbium', '158.92535', 12, 9,
        'Dy', 'Dysprosium', '162.5', 13, 9,
        'Ho', 'Holmium', '164.93032', 14, 9,
        'Er', 'Erbium', '167.259', 15, 9,
        'Tm', 'Thulium', '168.93421', 16, 9,
        'Yb', 'Ytterbium', '173.054', 3, 7,
        'Lu', 'Lutetium', '174.9668', 3, 9,
        'Hf', 'Hafnium', '178.49', 4, 6,
        'Ta', 'Tantalum', '180.94788', 5, 6,
        'W', 'Tungsten', '183.84', 6, 6,
        'Re', 'Rhenium', '186.207', 7, 6,
        'Os', 'Osmium', '190.23', 8, 6,
        'Ir', 'Iridium', '192.217', 9, 6,
        'Pt', 'Platinum', '195.084', 10, 6,
        'Au', 'Gold', '196.966569', 11, 6,
        'Hg', 'Mercury', '200.59', 12, 6,
        'Tl', 'Thallium', '204.3833', 13, 6,
        'Pb', 'Lead', '207.2', 14, 6,
        'Bi', 'Bismuth', '208.9804', 15, 6,
        'Po', 'Polonium', '(209)', 16, 6,
        'At', 'Astatine', '(210)', 17, 6,
        'Rn', 'Radon', '(222)', 18, 6,
        'Fr', 'Francium', '(223)', 1, 7,
        'Ra', 'Radium', '(226)', 2, 7,
        '71', '이범호', 'Coach', 4, 10,
        'Th', 'Thorium', '232.03806', 5, 10,
        'Pa', 'Protactinium', '231.0588', 6, 10,
        'U', 'Uranium', '238.02891', 7, 10,
        'Np', 'Neptunium', '(237)', 8, 10,
        'Pu', 'Plutonium', '(244)', 9, 10,
        'Am', 'Americium', '(243)', 10, 10,
        'Cm', 'Curium', '(247)', 11, 10,
        'Bk', 'Berkelium', '(247)', 12, 10,
        'Cf', 'Californium', '(251)', 13, 10,
        'Es', 'Einstenium', '(252)', 14, 10,
        'Fm', 'Fermium', '(257)', 15, 10,
        'Md', 'Mendelevium', '(258)', 16, 10,
        'No', 'Nobelium', '(259)', 4, 3,
        'Lr', 'Lawrencium', '(262)', 3, 10,
        'Rf', 'Rutherfordium', '(267)', 4, 7,
        'Db', 'Dubnium', '(268)', 5, 7,
        'Sg', 'Seaborgium', '(271)', 6, 7,
        'Bh', 'Bohrium', '(272)', 7, 7,
        'Hs', 'Hassium', '(270)', 8, 7,
        'Mt', 'Meitnerium', '(276)', 9, 7,
        'Ds', 'Darmstadium', '(281)', 10, 7,
        'Rg', 'Roentgenium', '(280)', 11, 7,
        'Cn', 'Copernicium', '(285)', 12, 7,
        '54', '양현종', 'Pitcher', 13, 7,
        'Fl', 'Flerovium', '(289)', 14, 7,
        'Mc', 'Moscovium', '(290)', 15, 7,
        'Lv', 'Livermorium', '(293)', 16, 7,
        'Ts', 'Tennessine', '(294)', 17, 7,
        'Og', 'Oganesson', '(294)', 18, 7
      ],
    };
  },
  mounted() {
    this.dynamicLoadCSS();
    this.init();
    this.animate();
    window.addEventListener('resize', this.onWindowResize);
  },
  beforeUnmount() {
    this.resetStyle();
    this.removeDynamicLoadCSS();
    window.removeEventListener('resize', this.onWindowResize);
  },
  methods: {
    init() {
      this.settingOnloadStyle();

      // 建立相機
      this.camera = new THREE.PerspectiveCamera(40, window.innerWidth / window.innerHeight, 1, 10000);
      this.camera.position.z = 3000;

      // 建立場景
      this.scene = new THREE.Scene();

      // Add placeholders for other transformations
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
    },
    /**
     * 設定載入時的樣式
     */
    settingOnloadStyle() {
      document.body.style.padding = "0";
      document.body.style.margin = "0";
      document.body.style.backgroundColor = "rgb(25, 25, 25)";
      document.body.style.color = "#fff";
      document.body.style.overscrollBehavior = "none";
    },
    /**
     * 重置樣式
     */
    resetStyle() {
      document.body.style.padding = "";
      document.body.style.margin = "";
      document.body.style.backgroundColor = "";
      document.body.style.color = "";
      document.body.style.overscrollBehavior = "";
    },
    /**
     * 動態載入 CSS
     */
    dynamicLoadCSS() {
      // 動態載入多個 CSS
      const styles = ['/css/three-ex05.css'];
      styles.forEach(path => {
        const link = document.createElement('link');
        link.rel = 'stylesheet';
        link.href = path;
        document.head.appendChild(link);
        this.dynamicCSS.push(link);
      });
    },
    /**
     * 移除動態載入的 CSS
     */
    removeDynamicLoadCSS() {
      this.dynamicCSS.forEach(link => {
        document.head.removeChild(link);
      });
    },
    /**
     * 建立表格目標
     */
    createTableTargets() {
      const tableTargets = [];

      for (let i = 0; i < this.table.length; i += 5) {
        // 創建元素的 HTML 結構
        const element = document.createElement('div');
        element.className = 'element';
        element.style.backgroundColor = `rgba(232, 0, 47, ${Math.random() * 0.5 + 0.25})`;

        // 元素號碼
        const number = document.createElement('div');
        number.className = 'number';
        number.textContent = i / 5 + 1;
        element.appendChild(number);

        // 元素符號
        const symbol = document.createElement('div');
        symbol.className = 'symbol';
        symbol.textContent = this.table[i];
        element.appendChild(symbol);

        // 元素詳細資訊
        const details = document.createElement('div');
        details.className = 'details';
        details.innerHTML = `${this.table[i + 1]}<br>${this.table[i + 2]}`;
        element.appendChild(details);

        // 3D CSS 物件
        const objectCSS = new CSS3DObject(element);

        // 設定物件的位置
        objectCSS.position.x = Math.random() * 4000 - 2000;
        objectCSS.position.y = Math.random() * 4000 - 2000;
        objectCSS.position.z = Math.random() * 4000 - 2000;
        this.scene.add(objectCSS);

        this.objects.push(objectCSS);
        
        const object = new THREE.Object3D(); // 建立一個新的 THREE.Object3D 物件
        object.position.x = this.table[i + 3] * 140 - 1330; // 設定物件在 x 軸的位置
        object.position.y = -(this.table[i + 4] * 180) + 990; // 設定物件在 y 軸的位置

        tableTargets.push(object);
      }

      return tableTargets;
    },
    /**
     * 建立球形目標
     */
    createSphereTargets() {
      const sphereTargets = [];
      const vector = new THREE.Vector3();
      const spherical = new THREE.Spherical();

      for (let i = 0, l = this.objects.length; i < l; i++) {
        const phi = Math.acos(-1 + (2 * i) / l);
        const theta = Math.sqrt(l * Math.PI) * phi;

        const object = new THREE.Object3D(); // 建立一個新的 THREE.Object3D 物件

        spherical.set(800, phi, theta); // 設定球體的半徑、緯度、經度
        object.position.setFromSpherical(spherical);  // 依照球體座標系統設定物件的位置
        vector.copy(object.position).multiplyScalar(2); // 複製物件的位置並乘以 2

        object.lookAt(vector);  // 物件看向 vector 的方向

        sphereTargets.push(object);
      }

      return sphereTargets;
    },
    /**
     * 建立螺旋目標
     */
    createHelixTargets() {
      const helixTargets = [];
      const vector = new THREE.Vector3();

      for (let i = 0, l = this.objects.length; i < l; i++) {
        const theta = i * 0.175 + Math.PI;
        const y = -(i * 8) + 450;

        const object = new THREE.Object3D(); // 建立一個新的 THREE.Object3D 物件

        object.position.setFromCylindricalCoords(900, theta, y); // 設定物件的位置，使用圓柱座標系統
        vector.x = object.position.x * 2; // 設定 vector 的 x 座標為物件 x 座標的兩倍
        vector.y = object.position.y; // 設定 vector 的 y 座標為物件 y 座標
        vector.z = object.position.z * 2; // 設定 vector 的 z 座標為物件 z 座標的兩倍

        object.lookAt(vector); // 使物件朝向 vector 所指的位置

        helixTargets.push(object);
      }

      return helixTargets;
    },
    /**
     * 建立格子目標
     */
    createGridTargets() {
      const gridTargets = [];

      for (let i = 0; i < this.objects.length; i++) {
        const object = new THREE.Object3D();

        // 設定物件位置
        object.position.x = (i % 5) * 400 - 800;
        object.position.y = -(Math.floor(i / 5) % 5) * 400 + 800;
        object.position.z = Math.floor(i / 25) * 1000 - 2000;

        gridTargets.push(object);
      }

      return gridTargets;
    },
    /**
     * 開始動畫循環
     */
    animate() {
      requestAnimationFrame(this.animate);  // 透過 requestAnimationFrame 循環
      TWEEN.update(); // 更新 TWEEN
      this.controls.update(); // 更新控制器
    },
    /**
     * 渲染場景
     */
    renderScene() {
      this.renderer.render(this.scene, this.camera);
    },
    /**
     * 當視窗大小改變時更新相機和渲染器
     */
    onWindowResize() {
      this.camera.aspect = window.innerWidth / window.innerHeight;  // 設定相機的寬高比
      this.camera.updateProjectionMatrix(); // 更新相機的投影矩陣
      this.renderer.setSize(window.innerWidth, window.innerHeight); // 設定渲染器的寬高
      this.renderScene();
    },
    /**
     * 轉換佈局
     * @param targets 目標位置
     * @param duration  持續時間
     */
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
    },
  },
};
</script>

<style scoped></style>