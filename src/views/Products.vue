<template>  
  <div class="block mx-auto w-full h-full">  
<div class="flex justify-between  text-white bac  p-6 "> 
      <h1 class="font-semibold leading-10">Products</h1>  
    
    <div class="relative"><p class=" font-black mb-2 ">Поиск по названию</p><input class="w-96 border-2 outline-none bor text-black " type="text" placeholder="search" v-model="searchString" /><i class="fas fa-search absolute right-2 bottom-1 text-black "></i>
       </div> 
    <div> 
      <p class="font-black mb-2">Поиск по ценам</p> 
      <input class="w-96 border-2  outline-none bor mr-4 text-black" type="text" placeholder="min" v-model.number="min" />  
    <input class="w-96 border-2  outline-none bor text-black" type="text" placeholder="max" v-model.number="max" />  
    </div> 
</div> 
    <div v-if="fetching">Loading...</div>  
    <div v-else-if="error">Oh no... {{ error }}</div>  
    <div class="container mx-auto" v-else>  
      <div v-if="data" class="flex flex-wrap mt-8 justify-center  w-full">  
        <div  
          v-for="p in data.products"  
          :key="p.id"  
          class="rounded-lg back hover:my-2  hover:rr  w-1/6 mx-5 mb-5 bg-slate-200 p-4"  
          @click="move(p.id)" >  
            <img class="" :src="'http://38.242.229.113:8055/assets/' + p.image.id" alt="">  
              <p class="text-lg text-black font-medium m-5 ">{{ p.title }}</p> 
       <div class="flex justify-between">
      <div class=""> 
      <p class="mt-8 items-stretch text-lg text-black font-medium ">Цена: <span class="font-bold text-xl">{{ p.price }}</span> </p>
     </div> 
      <button @click="addToCart(p)" class=" text-white cart p-4 rounded-xl"><svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
  <path stroke-linecap="round" stroke-linejoin="round" d="M3 3h2l.4 2M7 13h10l4-8H5.4M7 13L5.4 5M7 13l-2.293 2.293c-.63.63-.184 1.707.707 1.707H17m0 0a2 2 0 100 4 2 2 0 000-4zm-8 2a2 2 0 11-4 0 2 2 0 014 0z" />
</svg>
      </button>
    </div>
    </div> 
        </div>  
      </div>  
        <button class=" block mx-auto bac text-white p-5 font-semibold text-xl" @click="limit+=10">Show more</button>  
    </div>  
</template>
<script> 
import { useQuery, gql } from "@urql/vue"; 
import { useRouter } from "vue-router"; 
import { ref } from "@vue/reactivity"; 
import { addToCart } from '@/utils/cart'
export default { 
  setup() { 
    const limit = ref(10); 
    const router = useRouter(); 
    const searchString = ref(null); 
    const min = ref(0); 
    const max = ref(10000000); 
    const getProductsQuery = gql` 
     query ($search: String, $min: Float, $max: Float, $limit: Int! = 10) { 
        products( 
          search: $search 
          filter: { 
            _and: [{ price: { _gte: $min } }, { price: { _lte: $max } }] 
          } 
          limit: $limit 
        ) { 
          id 
          title 
          price 
          description 
          image { 
            id 
          } 
        } 
      } 
    `; 
    const move = (id) => router.push("/products/" + id); 
    const getProducts = useQuery({ 
      query: getProductsQuery, 
      variables: { search: searchString, min, max, limit }, 
    }); // TODO: rename to products 
    return { 
      fetching: getProducts.fetching, 
      data: getProducts.data, 
      error: getProducts.error, 
      addToCart,
      move, 
      searchString, 
      min, 
      max, 
      limit 
    }; 
  }, 
}; 
</script> 
 
<style scoped></style>