<script setup lang="ts">
import type { TransactionData } from '@/App.vue';

defineProps<{
  transactions: TransactionData[],
}>();

const emit = defineEmits(['transactionDeleted']);

function getListItemClass(transaction: TransactionData) {
  return transaction.amount < 0 ? 'minus' : 'plus';
}

function getAmountText(amount: number) {
  const sign = amount >= 0 ? '' : '-';
  return `${sign} $${Math.abs(amount)}`;
}

function deleteTransaction(transactionId: number) {
  emit('transactionDeleted', transactionId);
}
</script>

<template>
  <h3>History</h3>
  <ul v-if="transactions?.length" id="list" class="list">
    <li
      v-for="transaction in transactions"
      :key="transaction.id"
      :class="getListItemClass(transaction)"
    >
      {{ transaction.text }} <span>{{ getAmountText(transaction.amount) }}</span>
      <button class="delete-btn" @click="deleteTransaction(transaction.id)">x</button>
    </li>
  </ul>
  <div v-else>
    There are no transactions here yet
  </div>
</template>
