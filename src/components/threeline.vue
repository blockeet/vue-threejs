<template>
  <div id="line"></div>
</template>

<script>
import * as Three from 'three';

export default {
  name: 'threeline',
  methods: {
    init() {
      const oline = document.getElementById('line');
      const renderer = new Three.WebGLRenderer();
      renderer.setSize(window.innerWidth, window.innerHeight);
      oline.append(renderer.domElement);
      const camera = new Three.PerspectiveCamera(45, window.innerWidth
      / window.innerHeight, 1, 500);
      camera.position.set(0, 0, 100);
      camera.lookAt(0, 0, 0);
      const scene = new Three.Scene();
      const material = new Three.LineBasicMaterial({ color: 0x0000ff });
      const geometry = new Three.Geometry();
      geometry.vertices.push(new Three.Vector3(-10, 0, 0));
      geometry.vertices.push(new Three.Vector3(0, 10, 0));
      geometry.vertices.push(new Three.Vector3(10, 0, 0));
      const line = new Three.Line(geometry, material);
      scene.add(line);
      renderer.render(scene, camera);
    },
  },
  mounted() {
    this.init();
  },
};
</script>

<style lang="less">
  #line {
    height:500px;
  }
</style>
