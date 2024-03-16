<script setup lang="ts">
defineProps<{
  total: number
}>();
</script>

<template>
  <h4>Total Balance</h4>
  <h1 id="balance">${{ total }}</h1>
</template>
