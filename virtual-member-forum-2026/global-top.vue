<template>
  <div v-if="$slidev.nav.currentPage === 1" class="absolute top-10 inset-x-0 flex justify-center">
    <img src="/big-logo.png" class="w-120" />
  </div>
  <div v-else class="absolute top-10 left-2">
    <img src="/small-logo.png" class="h-8" />
  </div>
</template>
