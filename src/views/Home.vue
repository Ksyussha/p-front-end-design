<template>
  <div>
    <h1>Home</h1>
  </div>
</template>

<script>
import qs from "qs";
import { ref } from 'vue';
export default {
  name: "Home",
  setup() {
   const sort = ref("updatedAt:desc");
   return {
     sort,
   }
  },
};
</script>

<style scoped>
</style>