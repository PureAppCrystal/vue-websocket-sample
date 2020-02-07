/* eslint-disable vue/require-v-for-key */
<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png"/>
   
    <div id="content">
      <!-- <BasicSample style="display: none;"/> -->
      <br/>
      <StompSample1  style="display: none;"/>
      <br/>
      <StompSample2 style="display: none;"/>
      <br/>
      <StompSample3/>

    </div>

    
  </div>
</template>

<script>
import BasicSample from '@/components/BasicSample.vue';
import StompSample1 from '@/components/StompSample1.vue';
import StompSample2 from '@/components/StompSample2.vue';
import StompSample3 from '@/components/StompSample3.vue';
export default {
  name: 'app',
  components: {
    // BasicSample,
    StompSample1,
    StompSample2,
    StompSample3,
  },
  data() {
    return {
     
    }
  },
  computed: {

  },
  methods: {
    

  },
  created() {
    
  },
  mounted() {

  }
}
</script>

<style>
html, body {
  height: 100%;
  width: 100%;
  padding: 0px;
  margin: 0px;    
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  
  height:100%;
}

#content{
  height: auto;
}
</style>
