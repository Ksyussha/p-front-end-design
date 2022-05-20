<template>
  <div class="body">
    <div class="h1"><h1>Product Page</h1> <a href="#" class="h1a">♡</a></div>
    <div class="product-card">
      <div v-for="i in product?.data?.images" :key="i">
        <img :src="'http://localhost:1337' + i?.url" alt="" />
      </div>
    <div class="card-text">
      <p class="title">{{ product?.data?.title }}</p>
      <p class="description">{{ product?.data?.description }}</p>
      <div class="price-and-cart">
        <p class="price">{{ product?.data?.price }} $</p>
        </div>
        <a href="#"><button class="card-text-button">add to cart</button></a>
    </div>
    </div>
  </div>
</template>

<script>
import { useECommerceStore } from "@/stores/e-commerce";
import { storeToRefs } from "pinia";
import qs from "qs";
import { useRoute, useRouter } from "vue-router";
export default {
  setup() {
    const eCommerceStore = useECommerceStore();
    const route = useRoute();
    eCommerceStore.fetchProduct(+route.params.id)
    const { product } = storeToRefs(eCommerceStore);
   return {
      product,
      eCommerceStore
   }
   },
   name: "Product",
   }
    
</script>

<style scoped>
.body {
  width: 1200px;
  margin: 0 auto;
  background-color: #fff;
}
.h1 {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 50px;
}
.h1a {
  font-size: 24px;
  color: #2f313d;
  text-decoration: none;
}

.product-card {
  display: flex;
  padding: 30px 50px;
}
.product-card img {
  width: 400px;
  margin-right: 50px;
  border: 1px solid #2f313d;
}
.price-and-cart {
  display: flex;
  align-items: center;
}
.card-text-button {
  padding: 10px 20px;
  border: #ffffff solid 1px;
  background-color: #2f313d;
  border-radius: 4%;
  color: #f6f6f2;
  font-weight: 700;
  margin: 20px 0px;
  opacity: 0.8;
}
.card-text-button:hover {
  background-color: #f6f6f2;
  color: #2f313d;
  border: 1px solid #2f313d;
}
.title {
  font-size: 28px;
  font-weight: 700;
  margin-bottom: 32px;
}
.price {
  font-size: 33px;
  font-weight: 700;
  color: #2f313d;
  margin-right: 15px;
  margin-top: 0px;
}
.description{
  color: #2f313d;
  line-height: 1.6;
}
</style>