<template>
  <div>
    Override
    {{ data }}
  </div>
</template>

<script setup lang="ts">
let count = 0
let timeout = 0
const { data, refresh } = await useAsyncData(() => process.server ? Promise.resolve(1) : new Promise(resolve => setTimeout(() => resolve(++count), timeout)))

if (count || data.value !== 1) {
  throw new Error('Data should be unset')
}

timeout = 100
const p = refresh()

if (process.client && (count !== 0 || data.value !== 1)) {
  throw new Error('count should start at 0')
}

timeout = 0
await refresh({ override: true })

if (process.client && (count !== 1 || data.value !== 1)) {
  throw new Error('override should execute')
}

await p

if (process.client && (count !== 2 || data.value !== 1)) {
  throw new Error('cancelled promise should not affect data')
}
</script>
