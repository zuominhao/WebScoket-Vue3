<script setup>
import Main from '../components/chat/Main.vue'
</script>

<template>
  <div class="common-layout">
    <el-container>
    
        <el-main ><Main/></el-main>

    </el-container>
  </div>
</template>

<style>
.common-layout {
  display: flex;
  flex-direction: column;
  height: 100vh;
}
body{
  margin: 0;
}
.el-header{
  --el-header-padding: 0!important;
}
.el-main{
  --el-main-padding: 0!important;
}
</style>

