<script setup lang="ts">
import { NButton, NForm, NFormItem, NH1, NInput, NInputNumber } from 'naive-ui'
import { computed, ref } from 'vue'

function randInt(end: number): number {
  return Math.floor(Math.random() * end)
}

const num = ref(15)
const count = ref(3)
const regenerateSign = ref(true)
const output = computed(() => {
  // A trigger to apply computation.
  if (!regenerateSign.value)
    return 'dummy'

  const binary = Array.from(num.value.toString(2))
  const numbers = binary.map(
    (_, index) => index,
  ).filter(
    index => binary[index] === '1',
  ).map(
    index => 1 << index,
  )

  const expectedLength = count.value ?? 1

  // Ensure the numbers are enough.
  while (numbers.length < expectedLength)
    numbers.push(numbers[randInt(numbers.length)])

  while (numbers.length > expectedLength) {
    const dropIndex = randInt(numbers.length)
    const [num] = numbers.splice(dropIndex, 1)
    numbers[randInt(numbers.length)] |= num
  }

  return numbers.sort((a, b) => a - b).join(' | ')
})

function regenerate() {
  regenerateSign.value = false
  regenerateSign.value = true
}
</script>

<template>
  <NH1>Helize</NH1>
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
    <NFormItem label="Output">
      <NInput readonly :value="output" />
    </NFormItem>
  </NForm>
  <NButton @click="regenerate">
    Regenerate
  </NButton>
</template>
