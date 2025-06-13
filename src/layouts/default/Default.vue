<template>
  <v-app>
    <default-bar />

    <v-main>
      <Hypercarousel url="src/portfolio.json" />
    </v-main>
  </v-app>
</template>

<script lang="ts" setup>
  import DefaultBar from './AppBar.vue'
  import Hypercarousel from "@/components/Hypercarousel.vue";

  // import {defineComponent} from "vue";
  // export default defineComponent({
  //   components: {Hypercarousel}
  // })
</script>
