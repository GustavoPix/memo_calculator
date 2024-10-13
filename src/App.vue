<template>
  <div class="main-app">
    <MainMenu />
    <Calculator v-show="isCalculatorVisible" />
    <Setup v-show="isSetupVisible"/>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import MainMenu from './components/menus/MainMenu.vue';
import Calculator from './views/Calculator.vue';
import Setup from './views/Setup.vue';
import { Pages } from '@/enums/Pages';

export default defineComponent({
  components: {
    MainMenu,
    Calculator,
    Setup
  },
  data(){
    return {
      page: Pages.CALCULATOR
    }
  },
  computed: {
    isCalculatorVisible() {
      return this.page === Pages.CALCULATOR;
    },
    isSetupVisible() {
      return this.page === Pages.SETUP;
    }
  }
});
</script>

<style lang="scss">
@import './assets/scss/main.scss';

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
