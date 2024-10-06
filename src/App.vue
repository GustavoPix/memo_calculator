<script setup lang="ts">
import MainMenu from './components/menus/MainMenu.vue';
import Calculator from './views/Calculator.vue';
</script>

<template>
  <div class="main-app">
    <MainMenu />
    <Calculator />
  </div>
</template>

<style lang="scss">
@import './assets/scss/reset.scss';

body{
  background-color: #1e1f22;
  max-height: 100vh;
}

.main-app{
  display: flex;
  flex-direction: column;
  justify-content: stretch;
  max-height: 100%;
  min-height: 1px;
  height: 100%;
}

#app{
  height: 100vh;
  overflow-y: hidden;
}

</style>
