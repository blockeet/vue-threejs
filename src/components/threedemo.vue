<template>
    <div>
        <div id="container"></div>
        <button @click="create">click to create new box</button>
        <button @click="mclone">22</button>
    </div>
</template>

<script>
import * as Three from 'three';
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls';

const earth = require('../assets/earth_atmos_2048.jpg');

export default {
  name: 'ThreeTest',
  data() {
    return {
      camera: null,
      scene: null,
      renderer: null,
      mesh: null,
      mesh2: null,
    };
  },
  methods: {
    animate() {
      // requestAnimationFrame(this.animate);
      // this.mesh.rotation.x += 0.0001;
      // this.mesh.rotation.y += 0.002;
      this.renderer.render(this.scene, this.camera);
    },
    control(vm) {
      const controls = new OrbitControls(vm.camera, vm.renderer.domElement);
      controls.rotateSpeed = 1;
      controls.autoRotate = true;
      controls.autoRotateSpeed = 2;
      // console.log(controls);
      controls.addEventListener('change', () => { vm.renderer.render(vm.scene, vm.camera); });
    },
    create() {
      const geometry2 = new Three.IcosahedronGeometry(1);
      const material2 = new Three.MeshNormalMaterial();
      this.mesh2 = new Three.Mesh(geometry2, material2);
      console.log('ready');
      this.scene.add(this.mesh2);
      this.mesh2.position.x += 5;
      // this.mesh2.position.y += 2;
      // console.log(this.mesh2.position);
      // console.log(this.scene);
      this.renderer.render(this.scene, this.camera);
    },
    mclone() {
      this.mesh2 = this.mesh.clone();
      this.mesh.translateX(5);
      this.scene.add(this.mesh2);
      this.scale();
      this.renderer.render(this.scene, this.camera);
    },
    scale() {
      this.mesh.scale.set(2, 2, 2);
    },
    initscene() {
      const container = document.getElementById('container');
      this.camera = new Three.PerspectiveCamera(70, container.clientWidth
      / container.clientHeight, 0.01, 200);
      this.camera.position.set(-1, 15, 80);
      this.scene = new Three.Scene();
      this.camera.lookAt(this.scene.position);
      const ambient = new Three.AmbientLight(0xffffff);
      const point = new Three.PointLight(0xffffff);
      point.position.set(10, 10, 10);
      const axisHelper = new Three.AxesHelper(100);
      this.renderer = new Three.WebGLRenderer({ antialias: true });
      this.renderer.setSize(container.clientWidth, container.clientHeight);
      this.scene.add(point);
      this.scene.add(ambient);
      this.scene.add(axisHelper);
      container.appendChild(this.renderer.domElement);
    },
    curve() {
      const geometry = new Three.PlaneGeometry(50, 20, 2, 2);
      // const materialOne = new Three.MeshPhongMaterial({
      //   color: 0xffff3f,
      // });
      const textureLoader = new Three.TextureLoader();
      const texture = textureLoader.load(earth);
      // texture.wrapS = Three.RepeatWrapping;
      // texture.wrapT = Three.RepeatWrapping;
      // texture.repeat.set(4, 2);
      // texture.offset = new Three.Vector2(0.5, 0.5);
      texture.rotation = Math.PI / 4;
      texture.center.set(0.5, 0.5);
      console.log(texture.matrix);
      const materialTwo = new Three.MeshLambertMaterial({
        map: texture,
      });
      // const materialArr = [materialOne, materialOne, materialOne, materialOne, materialTwo,
      //   materialOne];
      this.mesh = new Three.Mesh(geometry, materialTwo);
      this.scene.add(this.mesh);
    },
  },
  mounted() {
    this.initscene();
    this.curve();
    // this.init();
    const vm = this;
    this.animate();
    this.control(vm);
  },
};
</script>
<style lang="stylus" scoped>
#container {
  height: 400px;
}
</style>
