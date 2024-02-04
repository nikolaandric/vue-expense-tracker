<template>
  <Header></Header>
  <div class="container">
    <Balance :total="+total"></Balance>
    <IncomeExpense :income="+income" :expense="+expense"></IncomeExpense>
    <TransactionList
      :transactions="transactions"
      @transactionDeleted="handleTransactionDeleted"
    ></TransactionList>
    <AddTransaction
      @transactionSubmitted="handleTransactionSubmitted"
    ></AddTransaction>
  </div>
</template>

<script setup>
import Header from './components/Header.vue';
import Balance from './components/Balance.vue';
import IncomeExpense from './components/IncomeExpense.vue';
import TransactionList from './components/TransactionList.vue';
import AddTransaction from './components/AddTransaction.vue';

import { useToast } from 'vue-toastification';

import { ref, computed, onMounted } from 'vue';

const toast = useToast();

const transactions = ref([]);

//Check LS for data
onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem('transactions'));

  if (savedTransactions) {
    transactions.value = savedTransactions;
  }
});

//Get total
const total = computed(() => {
  return transactions.value.reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0);
});

//Get income
const income = computed(() => {
  return transactions.value
    .filter((t) => t.amount > 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0)
    .toFixed(2);
});

//Get expenses
const expense = computed(() => {
  return transactions.value
    .filter((t) => t.amount < 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0)
    .toFixed(2);
});

//Transaction Submitted
const handleTransactionSubmitted = (data) => {
  transactions.value.push({
    id: generateUniqueId(),
    ...data,
  });
  saveTransactionsToLS();
  toast.success('Transaction added');
};

const generateUniqueId = () => {
  return Math.floor(Math.random() * 1000000);
};

//Transaction Deleted
const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter((t) => t.id !== id);
  saveTransactionsToLS();
  toast.success('Transaction deleted');
};

//Save to localStorage
const saveTransactionsToLS = () => {
  localStorage.setItem('transactions', JSON.stringify(transactions.value));
};
</script>
