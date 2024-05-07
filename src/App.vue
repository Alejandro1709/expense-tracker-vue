<template>
  <Header />
  <div class="container">
    <Balance :total="+total" />
    <IncomeExpenses :income="+income" :expense="+expense" />
    <TransactionList :transactions="transactions" :transactionDeleted="handleTransactionDeleted"/>
    <AddTransaction :transactionSubmitted="handleTransactionSubmitted" />
  </div>
</template>

<script setup>
  import Header from './components/Header.vue';
  import Balance from './components/Balance.vue';
  import IncomeExpenses from './components/IncomeExpenses.vue';
  import TransactionList from './components/TransactionList.vue';
  import AddTransaction from './components/AddTransaction.vue';

  import { ref, computed, onMounted } from 'vue';

  import { useToast } from 'vue-toastification';

  const toast = useToast()

  const transactions = ref([]);

  onMounted(() => {
    const savedTransactions = JSON.parse(localStorage.getItem('transactions'))

    if (savedTransactions) {
      transactions.value = savedTransactions
    }
  })

  // Get Total
  const total = computed(() => {
    return transactions.value.reduce((acc, transaction) => {
      return acc + transaction.amount
    }, 0)
  })

  // Get Income
  const income = computed(() => {
    return transactions.value
    .filter((t) => t.amount > 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount
    }, 0).toFixed(2)
  })

  // Get Expense
  const expense = computed(() => {
    return transactions.value
    .filter((t) => t.amount < 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount
    }, 0).toFixed(2)
  })

  // Add Transaction
  const handleTransactionSubmitted = (transactionData) => {
    transactions.value.push({
      id: generateUniqueId(),
      text: transactionData.text,
      amount: transactionData.amount
    })

    saveTransactionToLocalStorage()

    toast.success('Transaction Added!')
  }

  //Generate random id
  const generateUniqueId = () => {
    return Math.floor(Math.random() * 10000)
  }

  //Delete transaction
  const handleTransactionDeleted = (id) => {
    transactions.value = transactions.value.filter((t) => t.id !== id)

    saveTransactionToLocalStorage()
    
    toast.success('Transaction Deleted!!')
  }

  // Save to local storage
  const saveTransactionToLocalStorage = () => {
    localStorage.setItem('transactions', JSON.stringify(transactions.value))
  }
</script>