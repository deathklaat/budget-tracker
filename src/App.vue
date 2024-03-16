<script setup lang="ts">
import { ref, computed, onMounted } from 'vue';
import { useToast } from 'vue-toastification';

import AppHeader from '@/components/AppHeader.vue';
import UserBalance from '@/components/UserBalance.vue';
import IncomeExpenses from '@/components/IncomeExpenses.vue';
import TransactionList from '@/components/TransactionList.vue';
import AddTransaction from '@/components/AddTransaction.vue';

export type TransactionData = {
  id: number
  text: string
  amount: number
}

const LS_TRANSACTIONS_KEY = 'transactions';

const toast = useToast();

const transactions = ref<TransactionData[]>([]);

const total = computed(() => {
  const res = transactions.value
    .reduce((acc, transaction) => (acc + transaction.amount), 0)
    .toFixed(2);
  return parseFloat(res);
});

const income = computed(() => {
  const res = transactions.value
    .filter((transaction) => transaction.amount > 0)
    .reduce((acc, transaction) => (acc + transaction.amount), 0)
    .toFixed(2);
  return parseFloat(res);
});

const expenses = computed(() => {
  const res = transactions.value
    .filter((transaction) => transaction.amount < 0)
    .reduce((acc, transaction) => (acc + transaction.amount), 0)
    .toFixed(2);
  return parseFloat(res);
});

onMounted(() => {
  let savedTransactions;

  try {
    savedTransactions = JSON.parse(localStorage.getItem(LS_TRANSACTIONS_KEY) || '[]');
  } catch (e) {
    savedTransactions = [];
  }

  transactions.value = savedTransactions;
});

function generateUniqueId(): number {
  return transactions.value?.[transactions.value.length - 1]?.id + 1 || Date.now();
}

function handleTransactionSubmitted(transactionData: TransactionData) {
  transactionData.id = generateUniqueId();
  transactions.value.push(transactionData);

  saveTransactionsToLocalStorage();

  toast.success('Transaction added');
}

function handleTransactionDeleted(transactionId: number) {
  transactions.value = transactions.value.filter((transaction) => transaction.id !== transactionId);

  saveTransactionsToLocalStorage();

  toast.success('Transaction deleted');
}

function saveTransactionsToLocalStorage() {
  localStorage.setItem(LS_TRANSACTIONS_KEY, JSON.stringify(transactions.value));
}
</script>

<template>
  <AppHeader />
  <div class="container">
    <UserBalance :total="total" />
    <IncomeExpenses :income="income" :expenses="expenses" />
    <TransactionList :transactions="transactions" @transaction-deleted="handleTransactionDeleted" />
    <AddTransaction @transaction-submitted="handleTransactionSubmitted" />
  </div>
</template>
