<template>
<div class="cont">
  <div class="menu">
    <RouterLink :to="{name: 'home'}">Home</RouterLink> |
    <RouterLink :to="{name: 'products'}">About</RouterLink> |
    <RouterLink :to="{name: 'register'}">Registration</RouterLink> |
    <RouterLink :to="{name: 'login'}">Login</RouterLink>
    <RouterView></RouterView>
    </div>
  </div>
</template>

<script lang="ts">
export default {
  name: "App",
};
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Barlow:wght@100&display=swap');

div { 
  font-family: 'Rubik', sans-serif; 
  font-size: 14px; 
}
.cont{
  width: 1320px;
  margin:0 auto;
  }
  a{
   text-decoration:none;
   color: white;
  }
</style>
