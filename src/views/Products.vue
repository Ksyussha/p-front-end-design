<template>
  <div class="cont">
    <div class="background">
      <div class="h1">
        <h1>Products</h1>
      </div>
    </div>
    <div class="flex">
      <div>
        <div v-for="c in categories.data" :key="c.id" class="categories">
          <button @click="pushQueryToUrl({categoryId: c.id})" class="button">{{ c.title }}</button>
        </div>
      </div>
    </div>
     <div class="flexx">
        <input
          class="search"
          type="search"
          v-model="searchString"
          @input="pushQueryToUrl({search: searchString})"
          placeholder="Search... "
        />
      </div>
    <div class="products-and-filters">
      <div class="goods">
        <div class="good" v-for="p in products.data" :key="p.id" @click="moveProduct(p.id)" >
          <p class="title">{{ p.title }}</p>
          <div v-for="i in p.images" :key="i.id" class="images">
            <img :src="'http://localhost:1337' + i?.formats?.thumbnail?.url" alt />
          </div>
        </div>
      </div>
      <div class="filters">
        <h1>filters</h1>
        <p>
          <input
            type="checkbox"
            value="lidar:true"
            @change="pushQueryToUrl({ spec: $event.target.value })"
          />
          <label>lidar</label>
        </p>
        <p>
          <input
            type="checkbox"
            value="dualsim:true"
            @change="pushQueryToUrl({ spec: $event.target.value })"
          />
          <label>dualsim</label>
        </p>
        <p>
          <input
            type="checkbox"
            value="touchbar:true"
            @change="pushQueryToUrl({ spec: $event.target.value })"
          />
          <label>touchbar</label>
        </p>
        <select name="sort" v-model="sort" @change="asd($event)">
        <option value="updatedAt:desc">newest</option>
        <option value="price:asc">price: ascending</option>
        <option value="price:desc">price: descending</option>
      </select> 
      </div>
    </div>
    <div class="pagination">
      <div v-for="i in products?.meta?.pagination?.pageCount" :key="i" class="page">
        <button @click="pushQueryToUrl({ page: i })" class="pageButton">{{ i }}</button>
      </div>
    </div>
  </div>
</template>

<script>
import { useECommerceStore } from "@/stores/e-commerce";
import { useRoute, useRouter } from "vue-router";
import { ref, onMounted, watchEffect } from "vue";
import { storeToRefs } from "pinia";
import qs from "qs";
export default {
  setup() {
    const eCommerceStore = useECommerceStore();
    const { products } = storeToRefs(eCommerceStore);
    const { categories } = storeToRefs(eCommerceStore);
    const route = useRoute();
    const router = useRouter();
    const sort = ref(route.query.sort || "updatedAt:desc");
    const searchString = ref("");
    const spec = ref();
    let ezQuery = {};
    function asd(event) {
      pushQueryToUrl({ sort: event.target.value });
    }
    function pushQueryToUrl(queryParam) {
      Object.entries(queryParam).forEach(([key, value]) => {
        if (value) {
          if (key == "spec") {
            if (ezQuery[key] == undefined) {
              ezQuery[key] = value;
              console.log(1);
              console.log("ezQK", ezQuery[key]);
              console.log("eqq1", ezQuery);
            } else {
              console.log(11123, ezQuery[key]);
              // console.log('lol',ezQuery[key].map(s => s.join(':')+ ',' + value))
              // ezQuery[key] = ezQuery[key]+ ',' + value;
              ezQuery[key] = ezQuery[key].map(s => s.join(":")) + "," + value;

              // ezQuery[key] = queryParam.spec
              console.log(2);
              console.log("value", value);
              console.log("ezQuery", ezQuery);
              console.log("ezQK2", ezQuery[key]);
            }
          } else {
            ezQuery[key] = value;
          }
        } else {
          ezQuery[key] = undefined;
        }
      });
      console.log("qparam", queryParam);

      router.push({ query: ezQuery });
    }
    function moveProduct(id) {
      router.push(`/products/${id}`);
    }
    async function createProductQuery() {
      console.log("nado", ezQuery.spec);
      ezQuery = {
        page: route.query.page,
        gte: route.query.gte,
        lte: route.query.lte,
        sort: route.query.sort,
        categoryId: route.query.categoryId,
        spec: route.query.spec
          ? route.query.spec.split(",").map(s => s.split(":"))
          : undefined,
        search:
          route.query.search != "" || route.query.search
            ? route.query.search
            : undefined
      };
      console.log("ezQ.spec", ezQuery.spec);
      // console.log(ezQuery.spec)
      const pagination = { page: route.query.page || 1, pageSize: 4 };
      const populate = ["category", "images"];
      const sort = route.query.sort ? [route.query.sort] : ["updatedAt:desc"];
      const search =
        route.query.search != "" && route.query.search
          ? route.query.search
          : undefined;
      const spec = route.query.spec;
      console.log(spec);

      const filters = {
        $and: [
          {
            price: {
              $gte: route.query.price_gte
            }
          },
          {
            price: {
              $lte: route.query.price_lte
            }
          },
          {
            category: {
              id: {
                $eq: route.query.categoryId || categories[0]?.id
              }
            }
          },
          {
            title: {
              $containsi: search
            }
          }
        ]
      };
      if (ezQuery.spec) {
        ezQuery.spec.map(s => {
          filters.$and.push({
            spec: {
              $containsi: `"${s[0]}":${s[1]}`
            }
          });
        });
      }

      const query = {
        populate,
        pagination,
        sort,
        filters
      };
      await eCommerceStore.fetchCategories();
      await eCommerceStore.fetchProducts(query);
      console.log(filters);
    }
    watchEffect(() => {
      createProductQuery();
    });
    onMounted(() => {});
    return {
      products,
      eCommerceStore,
      pushQueryToUrl,
      categories,
      sort,
      moveProduct,
      asd,
      searchString
    };
  },
  name: "Products"
};
</script>

<style scoped>
.background {
  background-image: url("https://assets.hongkiat.com/uploads/buying-apple-products-asia/Macbook-Air.jpg");
  background-size: cover;
}
.products-and-filters {
  padding: 20px;
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
}
.search {
  margin: 20px 0px 0px 0px;
    height: 24px;
    width: 249px;
    margin-right: 19px;
}
.filters {
  border: 1px solid #2f313d;
  width: 15%;
  padding: 30px;
}
.filters h1 {
  font-size: 24px;
}
.products {
  display: flex;
  flex-wrap: wrap;
  width: 79%;
}
.images{
  margin-top: 64px;
}
.pagination {
  display: flex;
  margin-top: 100px;
}
.page {
  margin: 0px 0px 0px 10px;
}
.pageButton {
  background-color: #fff0;
  border: 1px solid #2f313d;
  width: 25px;
  height: 25px;
}
.categories {
  margin: 24px 0px;
  display: inline-block;
  display: inline-block;
}
.sortingNproducts {
  display: flex;
}
p {
  color: #2f313d;
}
.h1 {
  display: flex;
  justify-content: space-around;
  height: 450px;
  align-items: center;
}
.h1 h1 {
  color: white;
  font-size: -webkit-xxx-large;
  text-transform: uppercase;
}
h1 {
  color: #2f313d;
}
.flexx {
  display: flex;
  justify-content: flex-end;
  align-items: center;
}
.flex {
  display: flex;
  justify-content: space-around;
  align-items: center;
}
svg.h-6.w-6 {
  width: 18px;
}
.button {
  border: 1px solid transparent;
  background-color: #f5f5f5;
  padding: 10px 53px 10px 5px;
  padding: 10px;
  font-size: large;
  width: 149px;
}
.button:hover {
  border: 1px solid #000;
  cursor: pointer;
}
.cont {
  width: 1320px;
  margin: 0 auto;
  background-color: #f6f6f2;
  padding: 0px 0px 30px 0px;
}
.goods {
  display: flex;
}
.good {
  border: 1px solid transparent;
  margin: 0px 17px 0px 0px;
  background-color: white;
  width: 237px;
  text-align: center;
  padding-top: 4px;
  padding-bottom: 60px;
  cursor: pointer;
}
.good:hover {
  border: 1px solid #000;
}
</style>