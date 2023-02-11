<script setup lang="ts">
import { NForm, NFormItem, NInput, NH1 } from 'naive-ui'
import { computed, ref } from 'vue'

const num = ref('0')
const output = computed(() => String(Number(num.value) * 10))
</script>

<template>
  <NH1>Helize</NH1>
  <NForm label-placement="left" label-width="auto" size="large">
    <NFormItem label="Number">
      <NInput v-model:value="num" />
    </NFormItem>
    <NFormItem label="Output">
      <NInput readonly :value="output" />
    </NFormItem>
  </NForm>
</template>