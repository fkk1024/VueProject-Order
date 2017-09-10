<template>
  <div id="app">
    <leftNav class="LN"></leftNav>
    <div class="main">
      <router-view></router-view>
    </div>
  </div>
</template>

<script>
  import leftNav from './components/common/leftNav.vue'
export default {
  name: 'app',
  components:{
    leftNav
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  display: flex;
}
#app .LN{
  width: 66px;
  /*flex: 0  50px;*/
}
  .main{
    flex:1;
    /*float: left;*/
    /*width: 95%;*/
    background: #eff2f7;
    height: 100%;
    overflow: hidden;
  }
</style>
