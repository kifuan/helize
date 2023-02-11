<script setup lang="ts">
import {
  NA,
  NButton,
  NForm,
  NFormItem,
  NH1,
  NInput,
  NInputNumber,
  NP,
  NSpace,
  useMessage,
} from 'naive-ui'
import { useClipboard, useLocalStorage } from '@vueuse/core'
import { computed, ref } from 'vue'

function randIndex(end: number): number {
  return Math.floor(Math.random() * end)
}

const num = useLocalStorage('num', 15)
const count = useLocalStorage('count', 3)
const regenerateSign = ref(true)
const message = useMessage()
const { copy, isSupported } = useClipboard()

const binary = computed(() => {
  return num.value?.toString(2) ?? 'Invalid number'
})

const output = computed(() => {
  // A trigger to apply computation.
  if (!regenerateSign.value)
    return 'dummy'

  if (num.value === undefined || num.value === null)
    return 'Invalid number'

  if (!count.value)
    return 'Invalid count'

  const b = binary.value
  const numbers = Array.from(b).map(
    (_, index) => index,
  ).filter(
    index => b[index] === '1',
  ).map(
    index => 1 << (b.length - index - 1),
  )

  // Ensure the numbers are enough.
  while (numbers.length < count.value)
    numbers.push(numbers[randIndex(numbers.length)])

  while (numbers.length > count.value) {
    const dropIndex = randIndex(numbers.length)
    const [num] = numbers.splice(dropIndex, 1)
    numbers[randIndex(numbers.length)] |= num
  }

  while (randIndex(count.value) !== 0)
    numbers[randIndex(numbers.length)] |= numbers[randIndex(numbers.length)]

  return numbers.sort((a, b) => a - b).join(' | ')
})

function handleRegenerate() {
  regenerateSign.value = false
  regenerateSign.value = true
}

async function handleCopy() {
  if (!isSupported.value) {
    message.error('Your browser does not support Clipboard API')
    return
  }
  await copy(output.value)
  message.success('Copied')
}
</script>

<template>
  <NH1>Helize</NH1>
  <NP>Works better when serval "1"s in the binary.</NP>
  <NP>
    Repository link:
    <NA href="https://github.com/kifuan/helize" target="_blank">
      kifuan/helize
    </NA>
  </NP>
  <NForm label-placement="left" label-width="auto" size="large">
    <NFormItem label="Number">
      <NInputNumber
        v-model:value="num"
        placeholder="Please input a number"
        :style="{ width: '100%' }"
      />
    </NFormItem>
    <NFormItem label="Count">
      <NInputNumber
        v-model:value="count"
        placeholder="Please input the count"
        :style="{ width: '100%' }"
      />
    </NFormItem>
    <NFormItem label="Binary">
      <NInput readonly :value="binary" />
    </NFormItem>
    <NFormItem label="Output">
      <NInput readonly :value="output" />
    </NFormItem>
  </NForm>
  <NSpace justify="end">
    <NButton ghost type="primary" @click="handleRegenerate">
      Regenerate
    </NButton>
    <NButton ghost type="primary" @click="handleCopy">
      Copy
    </NButton>
  </NSpace>
</template>
