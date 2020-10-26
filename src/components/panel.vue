<template>
  <div id="info"></div>
</template>

<script>
import * as Three from 'three';
import Stats from 'stats.js';
import GUI from 'dat.gui';

export default {
  name: 'cloth',
  data() {
    return {
      scene: null,
      camera: null,
      renderer: null,
      GloObj: null,
    };
  },
  methods: {
    initGloObj() {
      this.GloObj.params = {
        enableWind: true,
        showBall: false,
        togglePins: this.togglePins,
      };
      this.GloObj.DAMPING = 0.03;
    },
  },
};
</script>
