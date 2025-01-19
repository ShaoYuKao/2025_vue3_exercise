<template>
  <div id="info">
    <a href="https://threejs.org" target="_blank" rel="noopener">three.js</a> css3d - periodic table.
  </div>
  <div id="container" ref="container"></div>
  <div id="menu">
    <!-- 使用 Vue 的 @click 綁定方法來監聽按鈕點擊事件，觸發不同佈局轉換 -->
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
  name: "ThreeJsEx05",
  data() {
    return {
      // 定義相機、場景、渲染器等核心 Three.js 物件
      camera: null,
      scene: null,
      renderer: null,
      controls: null,
      objects: [],  // 存儲場景中的 3D 物件
      targets: { table: [], sphere: [], helix: [], grid: [] },  // 不同佈局的目標位置
      table: [
        // 元素數據，包含符號、名稱、原子量及其佈局位置
				'H', 'Hydrogen', '1.00794', 1, 1,
				'He', 'Helium', '4.002602', 18, 1,
				'Li', 'Lithium', '6.941', 1, 2,
				'Be', 'Beryllium', '9.012182', 2, 2,
				'B', 'Boron', '10.811', 13, 2,
				'C', 'Carbon', '12.0107', 14, 2,
				'N', 'Nitrogen', '14.0067', 15, 2,
				'O', 'Oxygen', '15.9994', 16, 2,
				'F', 'Fluorine', '18.9984032', 17, 2,
				'Ne', 'Neon', '20.1797', 18, 2,
				'Na', 'Sodium', '22.98976...', 1, 3,
				'Mg', 'Magnesium', '24.305', 2, 3,
				'Al', 'Aluminium', '26.9815386', 13, 3,
				'Si', 'Silicon', '28.0855', 14, 3,
				'P', 'Phosphorus', '30.973762', 15, 3,
				'S', 'Sulfur', '32.065', 16, 3,
				'Cl', 'Chlorine', '35.453', 17, 3,
				'Ar', 'Argon', '39.948', 18, 3,
				'K', 'Potassium', '39.948', 1, 4,
				'Ca', 'Calcium', '40.078', 2, 4,
				'Sc', 'Scandium', '44.955912', 3, 4,
				'Ti', 'Titanium', '47.867', 4, 4,
				'V', 'Vanadium', '50.9415', 5, 4,
				'Cr', 'Chromium', '51.9961', 6, 4,
				'Mn', 'Manganese', '54.938045', 7, 4,
				'Fe', 'Iron', '55.845', 8, 4,
				'Co', 'Cobalt', '58.933195', 9, 4,
				'Ni', 'Nickel', '58.6934', 10, 4,
				'Cu', 'Copper', '63.546', 11, 4,
				'Zn', 'Zinc', '65.38', 12, 4,
				'Ga', 'Gallium', '69.723', 13, 4,
				'Ge', 'Germanium', '72.63', 14, 4,
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
				'Yb', 'Ytterbium', '173.054', 17, 9,
				'Lu', 'Lutetium', '174.9668', 18, 9,
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
				'Ac', 'Actinium', '(227)', 4, 10,
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
				'No', 'Nobelium', '(259)', 17, 10,
				'Lr', 'Lawrencium', '(262)', 18, 10,
				'Rf', 'Rutherfordium', '(267)', 4, 7,
				'Db', 'Dubnium', '(268)', 5, 7,
				'Sg', 'Seaborgium', '(271)', 6, 7,
				'Bh', 'Bohrium', '(272)', 7, 7,
				'Hs', 'Hassium', '(270)', 8, 7,
				'Mt', 'Meitnerium', '(276)', 9, 7,
				'Ds', 'Darmstadium', '(281)', 10, 7,
				'Rg', 'Roentgenium', '(280)', 11, 7,
				'Cn', 'Copernicium', '(285)', 12, 7,
				'Nh', 'Nihonium', '(286)', 13, 7,
				'Fl', 'Flerovium', '(289)', 14, 7,
				'Mc', 'Moscovium', '(290)', 15, 7,
				'Lv', 'Livermorium', '(293)', 16, 7,
				'Ts', 'Tennessine', '(294)', 17, 7,
				'Og', 'Oganesson', '(294)', 18, 7
			]
    };
  },
  methods: {
    init() {
      // 初始化相機，設置視角和距離
      this.camera = new THREE.PerspectiveCamera(40, window.innerWidth / window.innerHeight, 1, 10000);
      this.camera.position.z = 3000;

      // 初始化場景
      this.scene = new THREE.Scene();

      // 根據數據建立 table 佈局並添加到場景
      this.table.forEach((item, index) => {
        if (index % 5 === 0) {
          const element = document.createElement('div');
          element.className = 'element';
          element.style.backgroundColor = `rgba(0,127,127,${Math.random() * 0.5 + 0.25})`;

          // 創建並設置元素號碼
          const number = document.createElement('div');
          number.className = 'number';
          number.textContent = Math.floor(index / 5) + 1;
          element.appendChild(number);

          // 創建並設置元素符號
          const symbol = document.createElement('div');
          symbol.className = 'symbol';
          symbol.textContent = this.table[index];
          element.appendChild(symbol);

          // 創建並設置詳細資訊
          const details = document.createElement('div');
          details.className = 'details';
          details.innerHTML = `${this.table[index + 1]}<br>${this.table[index + 2]}`;
          element.appendChild(details);

          // 創建 CSS3D 物件並添加到場景
          const objectCSS = new CSS3DObject(element);
          objectCSS.position.x = Math.random() * 4000 - 2000;
          objectCSS.position.y = Math.random() * 4000 - 2000;
          objectCSS.position.z = Math.random() * 4000 - 2000;
          this.scene.add(objectCSS);

          this.objects.push(objectCSS);

          // 設置元素在 table 佈局中的目標位置
          const object = new THREE.Object3D();
          object.position.x = (this.table[index + 3] * 140) - 1330;
          object.position.y = -(this.table[index + 4] * 180) + 990;
          this.targets.table.push(object);
        }
      });

      // 初始化渲染器並掛載到 DOM
      this.renderer = new CSS3DRenderer();
      this.renderer.setSize(window.innerWidth, window.innerHeight);
      this.$refs.container.appendChild(this.renderer.domElement);

      // 初始化控制器，用於鼠標控制相機視角
      this.controls = new TrackballControls(this.camera, this.renderer.domElement);
      this.controls.minDistance = 500;
      this.controls.maxDistance = 6000;
      this.controls.addEventListener('change', this.render);

      // 創建其他佈局
      this.createLayouts();

      // 初始轉換為 helix 佈局
      this.transform(this.targets.helix, 2000);

      // 監聽視窗大小調整事件監聽器
      window.addEventListener('resize', this.onWindowResize);
    },
    /**
     * createLayouts 方法用於計算物件在 sphere、helix 和 grid 佈局中的目標位置。
     * 
     * - Sphere 佈局：計算每個物件在球面上的位置，並將其目標位置存儲在 this.targets.sphere 中。
     * - Helix 佈局：計算每個物件在螺旋線上的位置，並將其目標位置存儲在 this.targets.helix 中。
     * - Grid 佈局：計算每個物件在網格中的位置，並將其目標位置存儲在 this.targets.grid 中。
     * 
     * @method createLayouts
     */
    createLayouts() {
      // 用於計算物件目標位置的輔助向量
      const vector = new THREE.Vector3();

      // 為每個物件計算 sphere、helix 和 grid 佈局的目標位置
      this.objects.forEach((_, i) => {
        // Sphere 佈局
        const phi = Math.acos(-1 + (2 * i) / this.objects.length);
        const theta = Math.sqrt(this.objects.length * Math.PI) * phi;
        const objectSphere = new THREE.Object3D();
        objectSphere.position.setFromSphericalCoords(800, phi, theta);
        vector.copy(objectSphere.position).multiplyScalar(2);
        objectSphere.lookAt(vector);
        this.targets.sphere.push(objectSphere);

        // Helix 佈局
        const thetaHelix = i * 0.175 + Math.PI;
        const yHelix = -(i * 8) + 450;
        const objectHelix = new THREE.Object3D();
        objectHelix.position.setFromCylindricalCoords(900, thetaHelix, yHelix);
        vector.x = objectHelix.position.x * 2;
        vector.y = objectHelix.position.y;
        vector.z = objectHelix.position.z * 2;
        objectHelix.lookAt(vector);
        this.targets.helix.push(objectHelix);

        // Grid 佈局
        const objectGrid = new THREE.Object3D();
        objectGrid.position.x = ((i % 5) * 400) - 800;
        objectGrid.position.y = (-(Math.floor(i / 5) % 5) * 400) + 800;
        objectGrid.position.z = (Math.floor(i / 25)) * 1000 - 2000;
        this.targets.grid.push(objectGrid);
      });
    },
    /**
     * 將物件轉換到目標位置和旋轉角度，並設置動畫效果。
     * 
     * @param {Array} targets - 目標位置和旋轉角度的數組，每個元素包含 position 和 rotation 屬性。
     * @param {number} duration - 動畫持續時間，以毫秒為單位。
     * 
     * 此方法會停止所有當前的動畫，並為每個物件設置新的位置和旋轉動畫。
     * 動畫使用指數緩動效果 (Exponential.InOut)。
     * 最後，設置一個更新渲染的動畫。
     */
    transform(targets, duration) {
      // 停止所有當前的動畫
      TWEEN.removeAll();

      // 遍歷所有物件，為每個物件設置動畫
      this.objects.forEach((object, i) => {
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
      });

      // 渲染更新動畫
      new TWEEN.Tween(this)
        .to({}, duration * 2)
        .onUpdate(this.render)
        .start();
    },
    /**
     * 視窗大小調整事件監聽器
     */
    onWindowResize() {
      // 調整相機和渲染器的大小
      this.camera.aspect = window.innerWidth / window.innerHeight;

      // 更新投影矩陣
      this.camera.updateProjectionMatrix();

      // 設定渲染器的大小
      this.renderer.setSize(window.innerWidth, window.innerHeight);

      render();
    },
    animate() {
      // 使用 requestAnimationFrame 進行動畫更新
      requestAnimationFrame(this.animate);
      TWEEN.update(); // 更新所有動畫
      this.controls.update(); // 更新控制器
    },
    render() {
      // 渲染場景
      this.renderer.render(this.scene, this.camera);
    }
  },
  mounted() {
    // 初始化場景並開始動畫
    this.init();
    this.animate();
  },
};
</script>

<style scoped>

a {
  color: #8ff;
}

#menu {
  position: absolute;
  bottom: 20px;
  width: 100%;
  text-align: center;
}

.element {
  width: 120px;
  height: 160px;
  box-shadow: 0px 0px 12px rgba(0,255,255,0.5);
  border: 1px solid rgba(127,255,255,0.25);
  font-family: Helvetica, sans-serif;
  text-align: center;
  line-height: normal;
  cursor: default;
}

.element:hover {
  box-shadow: 0px 0px 12px rgba(0,255,255,0.75);
  border: 1px solid rgba(127,255,255,0.75);
}

.element .number {
  position: absolute;
  top: 20px;
  right: 20px;
  font-size: 12px;
  color: rgba(127,255,255,0.75);
}

.element .symbol {
  position: absolute;
  top: 40px;
  left: 0px;
  right: 0px;
  font-size: 60px;
  font-weight: bold;
  color: rgba(255,255,255,0.75);
  text-shadow: 0 0 10px rgba(0,255,255,0.95);
}

.element .details {
  position: absolute;
  bottom: 15px;
  left: 0px;
  right: 0px;
  font-size: 12px;
  color: rgba(127,255,255,0.75);
}

button {
  color: rgba(127,255,255,0.75);
  background: transparent;
  outline: 1px solid rgba(127,255,255,0.75);
  border: 0px;
  padding: 5px 10px;
  cursor: pointer;
}

button:hover {
  background-color: rgba(0,255,255,0.5);
}

button:active {
  color: #000000;
  background-color: rgba(0,255,255,0.75);
}
</style>