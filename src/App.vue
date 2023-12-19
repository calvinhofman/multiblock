<script setup>
import { RouterLink, RouterView } from 'vue-router'
</script>

<template>
  <header>
    <nav class="flex flex-row justify-between container mx-auto mt-8 items-center">
      <div class="">
        <RouterLink to="/">
          <img src="./assets/logo.png" alt="" srcset="">
        </RouterLink>
      </div>
      <div class="hidden flex-row text-white space-x-12 items-center font-bold lg:px-8 xl:px-0 lg:flex">
        <RouterLink class=" hover:text-[#bebebe]" to="/">PRODUCTS</RouterLink>
        <RouterLink class=" hover:text-[#bebebe]" to="/">MULTIBLOCK LABS</RouterLink>
        <RouterLink class=" hover:text-[#bebebe]" to="/">$MBLOCK</RouterLink>
        <RouterLink class=" hover:text-[#bebebe]" to="/">FAQS</RouterLink>
        <RouterLink class=" hover:text-[#bebebe]" to="/dapp">
          <div class="bg-[#04D7FC] hover:bg-[#04d7fcbb] p-2 rounded-lg text-black font-bold">
            GO TO DAPP
          </div>
        </RouterLink>
      </div>
    </nav>
  </header>
  <RouterView />
</template>
