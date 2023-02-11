<script setup lang="ts">
import { NMessageProvider } from 'naive-ui'
import Helize from './components/Helize.vue'
</script>

<template>
  <NMessageProvider>
    <Helize />
  </NMessageProvider>
</template>

<style>
#app {
  margin: 3rem;
  padding-top: 2rem;
}
</style>
