<template>
  <div id="app">
    <letNav></letNav>
    <div class="main">
      <router-view/>
    </div>
  </div>
</template>

<script>
import letNav from '@/components/common/letNav'
export default {
  name: 'App',
  components:{
    letNav
  }
}
</script>

<style>
#app {
  font-family: 'Microsoft YaHei','Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
.main{
  float: left;
  width: 95%;
  background-color: #eff2f7;
  height: 100%;
  overflow: hidden;
}
</style>
