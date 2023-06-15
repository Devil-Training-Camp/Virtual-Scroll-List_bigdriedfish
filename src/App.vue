<script setup>
import scrollList from './components/scrollList.vue'
import { options } from '@/utils.js'
</script>

<template>
  <scrollList :data="options" :caches="2" :fixed="true" :fixed-size="20"></scrollList>
</template>

<style scoped></style>
