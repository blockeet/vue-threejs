<template>
  <div class="home">
    <!-- <img alt="Vue logo" src="../assets/logo.png"> -->
    <!-- <HelloWorld msg="Welcome to Your Vue.js App"/> -->
    <!-- <threeline/> -->
    <ThreeTest/>
  </div>
</template>

<script>
// @ is an alias to /src
// import HelloWorld from '@/components/HelloWorld.vue';
// import threeline from '@/components/threeline.vue';
import ThreeTest from '@/components/threedemo.vue';

export default {
  name: 'Home',
  components: {
    // HelloWorld,
    // threeline,
    ThreeTest,
  },
};
</script>
