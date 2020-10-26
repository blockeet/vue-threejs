<template>
    <div id="container" style="width:100%;height:800px;"></div>
</template>

<script>
/* eslint-disable global-require */
import * as Three from 'three';
import Stats from 'stats.js';
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls';

const river = require('../assets/river.jpg');

const randomBuildingTexturesArr = {
  // eslint-disable-next-line global-require
  building1: require('../assets/building1.jpg'),
  building2: require('../assets/building2.jpg'),
  building3: require('../assets/building3.jpg'),
  building4: require('../assets/building4.jpg'),
  building5: require('../assets/building5.jpg'),
  building6: require('../assets/building6.jpg'),
  building7: require('../assets/building7.jpg'),
  building8: require('../assets/building8.jpg'),
  building9: require('../assets/building9.jpg'),
  building0: require('../assets/building10.jpg'),
};

const JMBody = require('../assets/JMtowerbody.jpg');
const JMTop = require('../assets/JMtowertop.jpg');

export default {
  name: 'Demo',
  data() {
    return {
      loader: new Three.TextureLoader(),
      MATERIAL_COLOR: 'rgb(120, 120, 120)',
      shanghaiTowerPosition: { x: 25, y: 17, z: -30 },
      globalFinancialCenterPosition: { x: 15, y: 0, z: -30 },
      jinmaoTowerPosition: { x: 18, y: 11, z: -20 },
      randomBuildingPositionsArr: [
        { x: -13, y: 0, z: -15 },
        { x: -7, y: 0, z: -13 },
        { x: -1, y: 0, z: -16 },
        { x: -2, y: 0, z: -10 },
        { x: -8, y: 0, z: -5 },
        { x: 5, y: 0, z: -25 },
        { x: -3, y: 0, z: -18 },
        { x: -8, y: 0, z: -18 },
        { x: -18, y: 0, z: -25 },
        { x: -6, y: 0, z: -25 },
        { x: -3, y: 0, z: -30 },
        { x: -10, y: 0, z: -30 },
        { x: -17, y: 0, z: -30 },
        { x: -3, y: 0, z: -35 },
        { x: -12, y: 0, z: -35 },
        { x: -20, y: 0, z: -35 },
        { x: -3, y: 0, z: -40 },
        { x: -16, y: 0, z: -40 },
        { x: 16, y: 0, z: -40 },
        { x: 18, y: 0, z: -38 },
        { x: 16, y: 0, z: -40 },
        { x: 30, y: 0, z: -40 },
        { x: 32, y: 0, z: -40 },
        { x: 16, y: 0, z: -35 },
        { x: 36, y: 0, z: -38 },
        { x: 42, y: 0, z: -32 },
        { x: 42, y: 0, z: -26 },
        { x: 35, y: 0, z: -20 },
        { x: 36, y: 0, z: -32 },
        { x: 25, y: 0, z: -22 },
        { x: 26, y: 0, z: -20 },
        { x: 19, y: 0, z: -8 },
        { x: 30, y: 0, z: -18 },
        { x: 25, y: 0, z: -15 },
        { x: 9, y: 0, z: -10 },
        { x: 1, y: 0, z: -9 },
        { x: 1, y: 0, z: -30 },
        { x: 0, y: 0, z: -35 },
        { x: 1, y: 0, z: -32 },
        { x: 8, y: 0, z: -5 },
        { x: 15, y: 0, z: -6 },
        { x: 5, y: 0, z: -40 },
        { x: 9, y: 0, z: -40 },
      ],
      scene: null,
      stats: null,
      clock: null,
      // gui: null,
      camera: null,
      renderer: null,
      // controls: null,
    };
  },
  methods: {
    init() {
      const container = document.getElementById('container');
      const width = container.clientWidth;
      const height = container.clientHeight;
      this.scene = new Three.Scene();
      this.stats = new Stats();
      this.clock = new Three.Clock();
      // this.gui = new dat.GUI();
      this.camera = new Three.PerspectiveCamera(45, width / height, 1, 1000);
      this.camera.position.set(0, 30, 90);
      this.camera.lookAt(new Three.Vector3(0, 0, 0));
      this.renderer = new Three.WebGLRenderer();
      this.renderer.setClearColor(this.MATERIAL_COLOR);
      this.renderer.shadowMap.enabled = true;
      this.renderer.shadowMap.type = Three.PCFShadowMap;
      this.renderer.setSize(width, height);
      container.appendChild(this.renderer.domElement);
      container.appendChild(this.stats.dom);
      // const axesHelper = new Three.AxesHelper(500);
      // const gridHelper = new Three.GridHelper(100, 100);
      // this.scene.add(axesHelper);
      // this.scene.add(gridHelper);
      const groundOne = this.getGroundFront();
      const groundTwo = this.getGroundBehind();
      const orientalPearl = this.getOrientalPearl();
      const shanghaiTower = this.getShanghaiTower(
        this.shanghaiTowerPosition.x,
        this.shanghaiTowerPosition.y,
        this.shanghaiTowerPosition.z,
      );
      const globalFinancialCenter = this.getShangHaiGlobalFinancialCenter(
        this.globalFinancialCenterPosition.x,
        this.globalFinancialCenterPosition.y,
        this.globalFinancialCenterPosition.z,
      );
      const jinmaoTower = this.getJinmaoTower();

      this.getBuilding(this.randomBuildingPositionsArr);

      const huangpuriver = this.getRiver();
      huangpuriver.name = 'river';
      this.scene.add(groundOne, groundTwo, orientalPearl, shanghaiTower,
        globalFinancialCenter, jinmaoTower, huangpuriver);
      // this.shadowTest();
      const spotLight = this.getSpotLight(1.2);
      spotLight.position.set(100, 100, 80);
      this.scene.add(spotLight);
      this.renderer.render(this.scene, this.camera);
      // this.update();
    },
    getGroundFront() {
      const shape = new Three.Shape();
      shape.moveTo(50, 0);
      shape.lineTo(-25, 0);
      shape.quadraticCurveTo(-10, 107, 50, 15);
      const extrudeGeometry = new Three.ExtrudeGeometry(shape, {
        depth: 3,
        steps: 2,
        bevelThickness: 0,
        bevelSize: 1,
      });
      const material = new Three.MeshLambertMaterial({ color: '#666' });
      const mesh = new Three.Mesh(extrudeGeometry, material);
      mesh.receiveShadow = true;
      mesh.rotation.x = Math.PI / 2;
      mesh.position.set(0, 0, -50);
      return mesh;
    },
    getGroundBehind() {
      const shape = new Three.Shape();
      shape.moveTo(45, 100);
      shape.lineTo(50, 100);
      shape.lineTo(50, 0);
      shape.lineTo(-50, 0);
      shape.lineTo(-50, 60);
      shape.bezierCurveTo(5, 15, 15, 5, 45, 100);
      const extrudeGeometry = new Three.ExtrudeGeometry(shape, {
        depth: 3,
        steps: 2,
        bevelThickness: 0,
        bevelSize: 1,
      });
      const material = new Three.MeshLambertMaterial({ color: 'gray' });
      const mesh = new Three.Mesh(extrudeGeometry, material);
      mesh.receiveShadow = true;
      mesh.rotation.x = Math.PI + Math.PI / 2;
      mesh.rotation.y = Math.PI;
      mesh.position.set(0, 0, 50);
      return mesh;
    },
    getOrientalPearl() {
      const orientalPearl = new Three.Object3D();
      const bottom = new Three.Object3D();
      const cylinderGeometryOne = new Three.CylinderGeometry(2, 2, 0.38, 30);
      const cylinderMaterialOne = new Three.MeshPhongMaterial({ color: this.MATERIAL_COLOR });
      const cylinderOne = new Three.Mesh(cylinderGeometryOne, cylinderMaterialOne);
      cylinderOne.castShadow = true;
      cylinderOne.receiveShadow = true;
      const cylinderGeometryTwo = new Three.CylinderGeometry(1.8, 1.8, 0.2, 30);
      const cylinderMaterialTwo = new Three.MeshPhongMaterial({ color: this.MATERIAL_COLOR });
      const cylinderTwo = new Three.Mesh(cylinderGeometryTwo, cylinderMaterialTwo);
      cylinderTwo.position.y = 0.28;
      cylinderTwo.castShadow = true;
      cylinderTwo.receiveShadow = true;
      const littleBottomCylinderGeometryOne = new Three.CylinderGeometry(
        0.15,
        0.15,
        3,
        30,
      );
      const littleBottomCylinderMaterialOne = new Three.MeshPhongMaterial({
        color: this.MATERIAL_COLOR,
      });
      const littleBottomCylinderOne = new Three.Mesh(
        littleBottomCylinderGeometryOne,
        littleBottomCylinderMaterialOne,
      );
      littleBottomCylinderOne.position.set(1.1, 1, 0.5);
      littleBottomCylinderOne.rotation.set(
        (Math.PI / 2) * 1.15,
        (Math.PI / 5.5) * 1.5,
        (Math.PI / 2.5) * 1.1,
      );
      const littleBottomCylinderGeometryTwo = new Three.CylinderGeometry(
        0.15,
        0.15,
        3,
        30,
      );
      const littleBottomCylinderMaterialTwo = new Three.MeshPhongMaterial({
        color: this.MATERIAL_COLOR,
      });
      const littleBottomCylinderTwo = new Three.Mesh(
        littleBottomCylinderGeometryTwo,
        littleBottomCylinderMaterialTwo,
      );
      littleBottomCylinderTwo.position.set(-0.05, 1, -1.35);
      littleBottomCylinderTwo.rotation.set(
        (Math.PI / 2) * 1.47,
        -(Math.PI / 5.5) * 1.35,
        (Math.PI / 2.5) * 0.05,
      );
      const littleBottomCylinderGeometryTre = new Three.CylinderGeometry(
        0.15,
        0.15,
        3,
        30,
      );
      const littleBottomCylinderMaterialTre = new Three.MeshPhongMaterial({
        color: this.MATERIAL_COLOR,
      });
      const littleBottomCylinderTre = new Three.Mesh(
        littleBottomCylinderGeometryTre,
        littleBottomCylinderMaterialTre,
      );
      littleBottomCylinderTre.position.set(-1, 1, 0.8);
      littleBottomCylinderTre.rotation.set(
        -(Math.PI / 2) * 1.25,
        (Math.PI / 5.5) * 1,
        -(Math.PI / 3.5) * 0.9,
      );
      const bottomCylinder = this.getBottomCylinder();
      bottom.add(
        cylinderOne,
        cylinderTwo,
        bottomCylinder,
        littleBottomCylinderOne,
        littleBottomCylinderTwo,
        littleBottomCylinderTre,
      );
      const body = new Three.Object3D();
      const bodyCylinderGeometryOne = new Three.CylinderGeometry(0.35, 0.35, 15, 30);
      const bodyCylinderMaterialOne = new Three.MeshPhongMaterial({
        color: this.MATERIAL_COLOR,
      });
      const bodyCylinderOne = new Three.Mesh(
        bodyCylinderGeometryOne,
        bodyCylinderMaterialOne,
      );
      bodyCylinderOne.position.set(0, 7, 0.8);
      const bodyCylinderGeometryTwo = new Three.CylinderGeometry(0.35, 0.35, 15, 30);
      const bodyCylinderMaterialTwo = new Three.MeshPhongMaterial({
        color: this.MATERIAL_COLOR,
      });
      const bodyCylinderTwo = new Three.Mesh(
        bodyCylinderGeometryTwo,
        bodyCylinderMaterialTwo,
      );
      bodyCylinderTwo.position.set(0.7, 7, -0.5);
      const bodyCylinderGeometryTre = new Three.CylinderGeometry(0.35, 0.35, 15, 30);
      const bodyCylinderMaterialTre = new Three.MeshPhongMaterial({
        color: this.MATERIAL_COLOR,
      });
      const bodyCylinderTre = new Three.Mesh(
        bodyCylinderGeometryTre,
        bodyCylinderMaterialTre,
      );
      bodyCylinderTre.position.set(-0.7, 7, -0.45);
      const torus1 = this.getTorus(0.8, 0.2, 16, 10, 2);
      const torus2 = this.getTorus(0.8, 0.2, 16, 10, 7.5);
      const torus3 = this.getTorus(0.8, 0.2, 16, 10, 8.6);
      const torus4 = this.getTorus(0.8, 0.2, 16, 10, 9.7);
      const torus5 = this.getTorus(0.8, 0.2, 16, 10, 10.8);
      const torus6 = this.getTorus(0.8, 0.2, 16, 10, 11.9);
      const torus7 = this.getTorus(0.8, 0.2, 16, 10, 13);

      const bigSphereGeometry = new Three.SphereGeometry(2, 32, 32);
      const bigSphereMaterial = new Three.MeshPhongMaterial({
        color: this.MATERIAL_COLOR,
      });
      const bigSphere = new Three.Mesh(bigSphereGeometry, bigSphereMaterial);
      bigSphere.position.y = 5;
      const middleSphereGeometry = new Three.SphereGeometry(1.5, 32, 32);
      const middleSphereMaterial = new Three.MeshPhysicalMaterial({
        color: this.MATERIAL_COLOR,
      });
      const middleSphere = new Three.Mesh(
        middleSphereGeometry,
        middleSphereMaterial,
      );
      middleSphere.position.y = 15;
      body.add(bodyCylinderOne);
      body.add(bodyCylinderTwo);
      body.add(bodyCylinderTre);
      body.add(torus1);
      body.add(torus2);
      body.add(torus3);
      body.add(torus4);
      body.add(torus5);
      body.add(torus6);
      body.add(torus7);
      body.add(bigSphere);
      body.add(middleSphere);
      const head = new Three.Object3D();
      const headCylinderGeometryOne = new Three.CylinderGeometry(0.3, 0.3, 2.5, 5);
      const headCylinderMaterialOne = new Three.MeshPhongMaterial({
        color: this.MATERIAL_COLOR,
      });
      const headCylinderOne = new Three.Mesh(headCylinderGeometryOne, headCylinderMaterialOne);
      headCylinderOne.position.y = 17.5;
      const headSphereGeometry = new Three.SphereGeometry(0.6, 32, 32);
      const headSphereMaterial = new Three.MeshPhongMaterial({
        color: this.MATERIAL_COLOR,
      });
      const headSphere = new Three.Mesh(headSphereGeometry, headSphereMaterial);
      headSphere.position.y = 19;

      const headCylinderGeometryTwo = new Three.CylinderGeometry(0.25, 0.2, 3, 5);
      const headCylinderMaterialTwo = new Three.MeshPhongMaterial({
        color: this.MATERIAL_COLOR,
      });
      const headCylinderTwo = new Three.Mesh(headCylinderGeometryTwo, headCylinderMaterialTwo);
      headCylinderTwo.position.y = 20;
      const headTorus1 = this.getTorus(0.15, 0.15, 16, 10, 21.5);

      const headCylinderGeometryTre = new Three.CylinderGeometry(0.15, 0.15, 2, 10);
      const headCylinderMaterialTre = new Three.MeshPhongMaterial({
        color: this.MATERIAL_COLOR,
      });
      const headCylinderTre = new Three.Mesh(headCylinderGeometryTre, headCylinderMaterialTre);
      headCylinderTre.position.y = 22.5;
      const headTorus2 = this.getTorus(0.13, 0.13, 16, 10, 23.5);

      const headCylinderGeometryFor = new Three.CylinderGeometry(0.1, 0.1, 3, 10);
      const headCylinderMaterialFor = new Three.MeshPhongMaterial({
        color: this.MATERIAL_COLOR,
      });
      const headCylinderFor = new Three.Mesh(headCylinderGeometryFor, headCylinderMaterialFor);
      headCylinderFor.position.y = 25;

      head.add(headSphere, headTorus1, headTorus2, headCylinderFor);
      head.add(headCylinderOne, headCylinderTwo, headCylinderTre);

      orientalPearl.add(bottom);
      orientalPearl.add(body);
      orientalPearl.add(head);
      orientalPearl.castShadow = true;
      orientalPearl.receiveShadow = true;

      return orientalPearl;
    },
    getJinmaoTower(x, y, z) {
      const jinmaoTower = new Three.Object3D();
      const geometry = new Three.BoxGeometry(1, 22, 6);
      const material = new Three.MeshPhongMaterial({
        color: this.MATERIAL_COLOR,
      });
      material.map = this.loader.load(randomBuildingTexturesArr.building5);
      material.map.wrapS = Three.RepeatWrapping;
      material.map.wrapT = Three.RepeatWrapping;
      material.map.repeat.set(0.35, 8);

      const cube1 = new Three.Mesh(geometry, material);
      const cube2 = new Three.Mesh(geometry, material);
      cube2.rotation.set(0, Math.PI / 2, 0);

      const towerBody = this.getJinmaoTowerBody();

      const towerTop = this.getJinmaoTowerTop();

      jinmaoTower.add(cube1, cube2, towerBody, towerTop);
      jinmaoTower.receiveShadow = true;
      jinmaoTower.position.set(x, y, z);

      return jinmaoTower;
    },
    getRiver() {
      const material = new Three.MeshStandardMaterial({
        color: this.MATERIAL_COLOR,
      });
      material.map = this.loader.load(river);
      material.bumpMap = this.loader.load(river);
      material.roughnessMap = this.loader.load(river);
      material.bumpScale = 0.01;
      material.metalness = 0.1;
      material.roughness = 0.7;
      material.transparent = true;
      material.opacity = 0.85;
      const geometry = new Three.PlaneGeometry(73, 100, 60, 60);
      material.side = Three.DoubleSide;
      const realRiver = new Three.Mesh(geometry, material);
      realRiver.receiveShadow = true;
      realRiver.rotation.x = Math.PI / 2;
      realRiver.rotation.z = Math.PI / 2;
      realRiver.position.z = -14.5;
      realRiver.position.y = -2;
      return realRiver;
    },
    getJinmaoTowerBody() {
      const towerBody = new Three.Object3D();
      const geometry1 = new Three.BoxGeometry(5, 7, 5);
      const geometry2 = new Three.BoxGeometry(4.5, 5.5, 4.5);
      const geometry3 = new Three.BoxGeometry(4, 4, 4);
      const geometry4 = new Three.BoxGeometry(3.5, 3, 3, 5);
      const geometry5 = new Three.BoxGeometry(3, 2, 3);
      const geometry6 = new Three.BoxGeometry(2.5, 1.5, 2.5);
      const geometry7 = new Three.BoxGeometry(2, 1.3, 2);
      const geometry8 = new Three.BoxGeometry(1.5, 1, 1.5);
      const material = new Three.MeshPhongMaterial({
        color: this.MATERIAL_COLOR,
      });
      material.map = this.loader.load(JMBody);
      material.map.wrapS = Three.RepeatWrapping;
      material.map.wrapT = Three.RepeatWrapping;
      material.map.repeat.set(0.5, 1);

      const cube1 = new Three.Mesh(geometry1, material);
      const cube2 = new Three.Mesh(geometry2, material);
      const cube3 = new Three.Mesh(geometry3, material);
      const cube4 = new Three.Mesh(geometry4, material);
      const cube5 = new Three.Mesh(geometry5, material);
      const cube6 = new Three.Mesh(geometry6, material);
      const cube7 = new Three.Mesh(geometry7, material);
      const cube8 = new Three.Mesh(geometry8, material);
      cube2.position.set(0, 5.5, 0);
      cube3.position.set(0, 9.5, 0);
      cube4.position.set(0, 12.5, 0);
      cube5.position.set(0, 14.5, 0);
      cube6.position.set(0, 16, 0);
      cube7.position.set(0, 17.3, 0);
      cube8.position.set(0, 18.3, 0);

      towerBody.add(cube1);
      towerBody.add(cube2);
      towerBody.add(cube3);
      towerBody.add(cube4);
      towerBody.add(cube5);
      towerBody.add(cube6, cube7);
      towerBody.add(cube8);

      return towerBody;
    },
    getJinmaoTowerTop() {
      const towerTop = new Three.Object3D();
      const geometry1 = new Three.BoxGeometry(3.8, 0.5, 3.8);
      const geometry2 = new Three.BoxGeometry(3, 0.5, 3);
      const geometry3 = new Three.BoxGeometry(2.2, 0.5, 3);
      const geometry4 = new Three.BoxGeometry(1.4, 0.5, 1.4);
      const cylinderGeometry = new Three.CylinderGeometry(0.1, 0.5, 5, 3);
      const material = new Three.MeshPhongMaterial({
        color: this.MATERIAL_COLOR,
      });
      material.map = this.loader.load(JMTop);
      const cube1 = new Three.Mesh(geometry1, material);
      const cube2 = new Three.Mesh(geometry2, material);
      const cube3 = new Three.Mesh(geometry3, material);
      const cube4 = new Three.Mesh(geometry4, material);
      const cylinder = new Three.Mesh(cylinderGeometry, material);

      cube2.position.set(0, 0.5, 0);
      cube3.position.set(0, 1, 0);
      cube4.position.set(0, 1.5, 0);
      cylinder.position.set(0, 2, 0);

      towerTop.add(cube1, cube2, cube3, cube4, cylinder);
      towerTop.position.set(0, 11, 0);
      towerTop.rotation.set(0, Math.PI / 4, 0);

      return towerTop;
    },
    getShangHaiGlobalFinancialCenter(x, y, z) {
      const ShangHaiGlobalFinancialCenter = new Three.Object3D();
      const GlobalFinancialCenterBottom = this.getGlobalFinancialCenterBottom();
      // eslint-disable-next-line max-len
      const GlobalFinancialCenterTriangularBodyTop = this.getGlobalFinancialCenterTriangularBodyTop();
      ShangHaiGlobalFinancialCenter.add(GlobalFinancialCenterBottom);
      ShangHaiGlobalFinancialCenter.add(GlobalFinancialCenterTriangularBodyTop);
      ShangHaiGlobalFinancialCenter.position.set(x, y, z);
      ShangHaiGlobalFinancialCenter.scale.set(0.81, 0.81, 0.81);
      ShangHaiGlobalFinancialCenter.castShadow = true;
      ShangHaiGlobalFinancialCenter.receiveShadow = true;

      return ShangHaiGlobalFinancialCenter;
    },
    getGlobalFinancialCenterBottom() {
      const vertices = [
        new Three.Vector3(3, 0, 3), // 0
        new Three.Vector3(3, 0, -3), // 1
        new Three.Vector3(-3, 0, 3), // 2
        new Three.Vector3(-3, 0, -3), // 3
        // 中部
        new Three.Vector3(3, 10, 3), // 4
        new Three.Vector3(-3, 10, -3), // 5
        // 上部
        new Three.Vector3(-1.5, 30, 3), // 6
        new Three.Vector3(3, 30, -1.5), // 7
        new Three.Vector3(3, 30, -3), // 8
        new Three.Vector3(1.5, 30, -3), // 9
        new Three.Vector3(-3, 30, 1.5), // 10
        new Three.Vector3(-3, 30, 3),
      ];
      const faces = [
        new Three.Face3(0, 1, 2),
        new Three.Face3(3, 2, 1),
        // 每个面的 3个三角形
        // 1.
        new Three.Face3(6, 2, 0),
        new Three.Face3(0, 4, 6),
        new Three.Face3(11, 2, 6),
        // 2.
        new Three.Face3(0, 1, 7),
        new Three.Face3(7, 4, 0),
        new Three.Face3(8, 7, 1),
        // 3.
        new Three.Face3(1, 3, 9),
        new Three.Face3(9, 8, 1),
        new Three.Face3(3, 5, 9),
        // 4.
        new Three.Face3(10, 3, 2),
        new Three.Face3(11, 10, 2),
        new Three.Face3(10, 5, 3),
        // 顶部4个三角形
        new Three.Face3(6, 10, 11),
        new Three.Face3(7, 8, 9),
        new Three.Face3(6, 7, 10),
        new Three.Face3(7, 9, 10),
        // 两个剖面 三角形
        new Three.Face3(7, 6, 4),
        new Three.Face3(10, 9, 5),
      ];
      const globalGeomatryBottom = new Three.Geometry();
      globalGeomatryBottom.vertices = vertices;
      globalGeomatryBottom.faces = faces;
      globalGeomatryBottom.computeFaceNormals();
      const material = new Three.MeshPhongMaterial({
        color: 'rgb(12, 20, 30)',
      });
      const globalFinancialCenter = new Three.Mesh(globalGeomatryBottom, material);
      return globalFinancialCenter;
    },
    getGlobalFinancialCenterTriangularBodyTop() {
      const globalFinancialCenterBodyTop = new Three.Object3D();
      const vertices1 = [
        new Three.Vector3(-3, 30, 1.5),
        new Three.Vector3(-3, 35, 3),
        new Three.Vector3(-3, 30, 3),
        new Three.Vector3(-1.5, 30, 3),
        new Three.Vector3(-2, 35, 2),
      ];
      const faces1 = [
        new Three.Face3(1, 2, 3),
        new Three.Face3(1, 3, 4),
        new Three.Face3(0, 4, 3),
        new Three.Face3(1, 4, 0),
        new Three.Face3(1, 0, 2),
      ];
      const triangularGeometryBodyTop1 = new Three.Geometry();
      triangularGeometryBodyTop1.vertices = vertices1;
      triangularGeometryBodyTop1.faces = faces1;
      triangularGeometryBodyTop1.computeFaceNormals();
      const material = new Three.MeshPhongMaterial({
        color: 'rgb(12, 20, 30)',
      });
      const triangularBodyTop1 = new Three.Mesh(
        triangularGeometryBodyTop1,
        material,
      );
      const goneVertices1 = [
        new Three.Vector3(-3, 30, 1.5),
        new Three.Vector3(-2.5, 30, 1),
        new Three.Vector3(-1, 30, 2.5),
        new Three.Vector3(-1.5, 30, 3),
        new Three.Vector3(-2.5, 35, 2.5),
        new Three.Vector3(-2, 35, 2),
      ];
      const goneFaces1 = [
        new Three.Face3(0, 2, 3),
        new Three.Face3(0, 1, 2),
        new Three.Face3(0, 4, 3),
        new Three.Face3(5, 2, 1),
        new Three.Face3(4, 3, 2),
        new Three.Face3(4, 2, 5),
        new Three.Face3(1, 0, 4),
        new Three.Face3(4, 5, 1),
      ];
      const goneGeometry1 = new Three.Geometry();
      goneGeometry1.vertices = goneVertices1;
      goneGeometry1.faces = goneFaces1;
      goneGeometry1.computeFaceNormals();
      const gone1 = new Three.Mesh(goneGeometry1, material);
      const vertices2 = [
        new Three.Vector3(3, 30, -1.5),
        new Three.Vector3(1.5, 30, -3),
        new Three.Vector3(2, 35, -2),
        new Three.Vector3(3, 35, -3),
        new Three.Vector3(3, 30, -3),
      ];
      const faces2 = [
        new Three.Face3(4, 3, 0),
        new Three.Face3(3, 2, 0),
        new Three.Face3(3, 4, 1),
        new Three.Face3(2, 3, 1),
        new Three.Face3(0, 2, 1),
      ];
      const triangularGeometryBodyTop2 = new Three.Geometry();
      triangularGeometryBodyTop2.vertices = vertices2;
      triangularGeometryBodyTop2.faces = faces2;
      triangularGeometryBodyTop2.computeFaceNormals();
      const triangularBodyTop2 = new Three.Mesh(
        triangularGeometryBodyTop2,
        material,
      );
      const goneVertices2 = [
        new Three.Vector3(3, 30, -1.5), // 0
        new Three.Vector3(1.5, 30, -3), // 1
        new Three.Vector3(2, 35, -2), // 2
        new Three.Vector3(2.5, 35, -2.5), // 3
        new Three.Vector3(2.5, 30, -1), // 4
        new Three.Vector3(1, 30, -2.5),
      ];
      const goneFaces2 = [
        new Three.Face3(0, 2, 4),
        new Three.Face3(4, 2, 3),
        new Three.Face3(1, 2, 3),
        new Three.Face3(3, 5, 1),
        new Three.Face3(5, 4, 3),
        new Three.Face3(4, 0, 1),
        new Three.Face3(1, 5, 2),
        new Three.Face3(4, 5, 1),
        new Three.Face3(5, 2, 3),
      ];
      const goneGeometry2 = new Three.Geometry();
      goneGeometry2.vertices = goneVertices2;
      goneGeometry2.faces = goneFaces2;
      goneGeometry2.computeFaceNormals();
      const gone2 = new Three.Mesh(
        goneGeometry2,
        material,
      );
      const goneTopGeometry = new Three.PlaneGeometry(8, 2, 32);
      const gonemMaterial = new Three.MeshPhongMaterial({
        color: 'rgb(12, 20, 30)',
        side: Three.DoubleSide,
      });
      const goneTop = new Three.Mesh(goneTopGeometry, gonemMaterial);
      goneTop.position.set(0, 34, 0);
      goneTop.rotation.set(0, Math.PI / 4, 0);
      globalFinancialCenterBodyTop.add(triangularBodyTop1);
      globalFinancialCenterBodyTop.add(gone1);
      globalFinancialCenterBodyTop.add(triangularBodyTop2);
      globalFinancialCenterBodyTop.add(gone2);
      globalFinancialCenterBodyTop.add(goneTop);

      return globalFinancialCenterBodyTop;
    },
    /* eslint no-param-reassign: ["error", { "props": false }] */
    getShanghaiTower(x, y, z) {
      const geometry = new Three.CylinderGeometry(2, 3, 18, 7, 50);
      geometry.vertices.forEach((vertex, ind) => {
        vertex.z += Math.sin((vertex.y + ind) * 0.015);
        vertex.x += Math.sin((vertex.y + ind) * 0.01) * 1;
        if (vertex.y >= 8.5) {
          vertex.y -= vertex.x * 0.2;
        }
      });
      geometry.verticesNeedUpdate = true;
      geometry.wireframe = true;
      const material = new Three.MeshPhongMaterial({
        color: this.MATERIAL_COLOR,
      });
      material.map = this.loader.load(randomBuildingTexturesArr.building5);
      material.map.wrapS = Three.RepeatWrapping;
      material.map.wrapT = Three.RepeatWrapping;
      material.map.repeat.set(2, 9);
      const tower = new Three.Mesh(geometry, material);
      tower.position.set(x, y, z);
      tower.scale.set(1, 2, 0.5);
      tower.castShadow = true;
      tower.receiveShadow = true;
      return tower;
    },
    getBuilding(positionArr) {
      const defaultLength = 16;
      for (let i = 0; i < positionArr.length; i += 1) {
        const w = Math.random() * 3 + 2;
        const d = Math.random() * 3 + 2;
        const h = Math.random() * defaultLength + 2;
        const rh = h < 3 ? h + 3 : h;
        const geometry = new Three.BoxGeometry(w, rh, d);
        const material = new Three.MeshPhongMaterial({ color: this.MATERIAL_COLOR });
        const textureInd = Math.floor(Math.random() * 10);
        const Ind = `building${textureInd}`;
        // console.log('ind', Ind);
        const texture = this.loader.load(
          randomBuildingTexturesArr[Ind],
        );
        texture.wrapS = Three.RepeatWrapping;
        texture.wrapT = Three.RepeatWrapping;
        texture.repeat.set(1, rh / 2);
        const caizhi = [
          new Three.MeshPhongMaterial({ map: texture }),
          new Three.MeshPhongMaterial({ map: texture }),
          new Three.MeshPhongMaterial({ map: texture }),
          material,
          new Three.MeshPhongMaterial({ map: texture }),
          new Three.MeshPhongMaterial({ map: texture }),
        ];
        const mesh = new Three.Mesh(geometry, caizhi);
        mesh.position.set(
          positionArr[i].x,
          positionArr[i].y + rh / 2,
          positionArr[i].z,
        );
        mesh.castShadow = true;
        mesh.receiveShadow = true;
        this.scene.add(mesh);
      }
    },
    getSpotLight(intensity) {
      const SHADOW_MAPSIZE = 3072;
      const light = new Three.PointLight(0xffffff, intensity);
      light.castShadow = true;
      light.receiveShadow = true;
      light.shadow.bias = 0.0005;
      light.shadow.mapSize.width = SHADOW_MAPSIZE;
      light.shadow.mapSize.height = SHADOW_MAPSIZE;

      // light.shadow.camera.near = 1;
      // light.shadow.camera.far = 500;
      // light.shadow.camera.fov = 20;

      return light;
    },
    getBottomCylinder() {
      const object = new Three.Object3D();

      const bottomCylinderGeometry1 = new Three.CylinderGeometry(0.2, 0.2, 7, 30);
      const bottomCylinderMaterial1 = new Three.MeshPhongMaterial({
        color: this.MATERIAL_COLOR,
      });
      const bottomCylinder1 = new Three.Mesh(bottomCylinderGeometry1, bottomCylinderMaterial1);
      const bottomCylinder2 = new Three.Mesh(bottomCylinderGeometry1, bottomCylinderMaterial1);
      const bottomCylinder3 = new Three.Mesh(bottomCylinderGeometry1, bottomCylinderMaterial1);

      const sphere1 = this.getBottomSphere();
      const sphere2 = this.getBottomSphere();
      const sphere3 = this.getBottomSphere();
      bottomCylinder1.add(sphere1);
      bottomCylinder2.add(sphere2);
      bottomCylinder3.add(sphere3);

      bottomCylinder1.position.set(2, 2, 1);
      bottomCylinder1.rotation.set(-(Math.PI / 5.5), Math.PI / 10, Math.PI / 5);
      bottomCylinder2.position.set(0, 2, -2.5);
      bottomCylinder2.rotation.set(Math.PI / 4.5, Math.PI / 6, -Math.PI / 1);
      bottomCylinder3.position.set(-2, 2, 1.5);
      bottomCylinder3.rotation.set(
        -Math.PI / 15,
        Math.PI / 8,
        (-Math.PI / 10) * 2,
      );
      object.add(bottomCylinder1, bottomCylinder2, bottomCylinder3);

      return object;
    },
    getBottomSphere() {
      const sphereGeometry = new Three.SphereGeometry(0.32, 32, 32);
      const sphereMaterial = new Three.MeshPhongMaterial({
        color: this.MATERIAL_COLOR,
      });
      const sphere = new Three.Mesh(sphereGeometry, sphereMaterial);

      return sphere;
    },
    getTorus(a, b, c, d, y) {
      const torusGeometry = new Three.TorusGeometry(a, b, c, d);
      const torusMaterial = new Three.MeshPhongMaterial({
        color: this.MATERIAL_COLOR,
      });
      const torus = new Three.Mesh(torusGeometry, torusMaterial);
      torus.radius = 8;
      torus.tube = 2;
      torus.radialSegments = 7;
      torus.tubularSegments = 30;
      torus.rotation.x = Math.PI / 2;
      torus.position.y = y;

      return torus;
    },
    Fcontrols() {
      const vm = this;
      const controls = new OrbitControls(this.camera, this.renderer.domElement);
      controls.rotateSpeed = 1;
      controls.autoRotate = true;
      controls.autoRotateSpeed = 2;
      controls.addEventListener('change', () => {
        // const elapsedTime = vm.clock.getElapsedTime();
        // const plane = vm.scene.getObjectByName('river');
        // const planeGeo = plane.geometry;
        // planeGeo.vertices.forEach((vertex, ind) => {
        //   vertex.z = Math.sin(elapsedTime + ind * 0.3) * 0.5;
        // });
        // planeGeo.verticesNeedUpdate = true;
        vm.renderer.render(vm.scene, vm.camera);
        this.stats.update();
      });
    },
    /* eslint no-param-reassign: ["error", { "props": false }] */
    update() {
      this.stats.update();
      this.controls.update();
      // const elapsedTime = this.clock.getElapsedTime();
      // const plane = this.scene.getObjectByName('river');
      // const planeGeo = plane.geometry;
      // planeGeo.vertices.forEach((vertex, ind) => {
      //   vertex.z = Math.sin(elapsedTime + ind * 0.3) * 0.5;
      // });
      // planeGeo.verticesNeedUpdate = true;
      this.renderer.render(this.scene, this.camera);
      // console.log(this.scene);
      // console.log('renderer', this.renderer.domElement);
      requestAnimationFrame(this.update);
    },
    shadowTest() {
      // const container = document.getElementById('container');
      // const width = container.clientWidth;
      // const height = container.clientHeight;
      // this.stats = new Stats();
      // this.scene = new Three.Scene();
      // // 创建相机
      // this.camera = new Three.PerspectiveCamera( // 透视投影相机
      //   40, // 视场，表示能够看到的角度范围
      //   width / height, // 渲染窗口的长宽比，设置为浏览器窗口的长宽比
      //   0.1, // 从距离相机多远的位置开始渲染
      //   2000, // 距离相机多远的位置截止渲染
      // );
      // this.camera.position.set(-10, 10, 40); // 设置相机位置
      // // 创建渲染器
      // this.renderer = new Three.WebGLRenderer({
      //   antialias: true, // 是否执行抗锯齿
      // });
      // // this.renderer.setPixelRatio(window.devicePixelRatio); // 设置设备像素比率。通常用于HiDPI设备，以防止输出画布模糊。
      // this.renderer.setSize(width, height); // 设置渲染器大小
      // this.renderer.shadowMap.enabled = true;
      // container.appendChild(this.renderer.domElement);

      // // 创建物体
      // const geometry = new Three.BoxBufferGeometry(4, 4, 4); // 生成几何体
      // const material = new Three.MeshLambertMaterial({
      //   // 生成材质
      //   color: 0x00ff00,
      // });
      // const mesh = new Three.Mesh(geometry, material); // 生成网格
      // mesh.castShadow = true; // 对象是否渲染到阴影贴图中，默认值为false
      // mesh.position.set(0, 3, 0); // 设置物体位置
      // this.scene.add(mesh); // 添加到场景中

      // // 创建平面
      // const planeGeometry = new Three.PlaneGeometry(300, 300); // 生成平面几何
      // const planeMaterial = new Three.MeshLambertMaterial({
      //   // 生成材质
      //   color: 0xcccccc,
      // });
      // const planeMesh = new Three.Mesh(planeGeometry, planeMaterial); // 生成平面网格
      // planeMesh.receiveShadow = true; // 设置平面网格为接受阴影的投影面
      // planeMesh.rotation.x = -Math.PI / 2; // 绕X轴旋转90度
      // this.scene.add(planeMesh); // 添加到场景中

      const light = new Three.DirectionalLight(0xffffff, 1);
      light.position.set(-40, 40, 20);
      light.castShadow = true;
      // light.target = mesh;
      this.scene.add(light);
      // this.renderer.render(this.scene, this.camera);
    },
  },
  mounted() {
    this.init();
    // this.shadowTest();
    this.Fcontrols();
  },
};

</script>
