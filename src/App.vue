
<template>
  <Header />

  <div class="container">
    <Balance :total="+total"/>
    <IncomeExpense :income="+income" :expense="+expense"/>
    <TransactionList :transactions ="transactions" @transactionDeleted="handleTransactionDeleted"/>
    <AddTransaction @transactionSubmitted="handleTransactionSubmitted"/>
  </div>
</template>

<script setup>
  import Header from './components/Header.vue';
  import Balance from './components/Balance.vue';
  import IncomeExpense from './components/IncomeExpense.vue';
  import TransactionList from './components/TransactionList.vue';
  import AddTransaction from './components/AddTransaction.vue';
  import { ref, computed, onMounted } from 'vue'

  import { useToast } from 'vue-toastification'

  const toast = useToast();

  const transactions = ref([]);

  onMounted(() => {
    const savedTransactions = JSON.parse(localStorage.getItem('transactions'))

    if (savedTransactions) {
       transactions.value = savedTransactions;
    }
  })

  const total = computed(() => {
    return transactions.value.reduce((sum, transaction) => {
      return sum + transaction.amount}, 0)
    });

  const income = computed(() => {
    return transactions.value.filter((transaction) => transaction.amount > 0)
      .reduce((sum , transaction) => {
         return sum + transaction.amount
    }, 0)
    .toFixed(2)
  })

  const expense = computed(() => {
    return transactions.value.filter((transaction) => transaction.amount < 0)
      .reduce((sum, transaction) => {
         return sum + transaction.amount
    }, 0).toFixed(2)
  })

  const handleTransactionSubmitted = (transactionData) => {
    transactions.value.push({
      id: generateUniqueId(),
      text: transactionData.text,
      amount: transactionData.amount,
    })

    savedTransactionsToLocalStorage();
   toast.success('Transaction Added successfully')

  }

  const handleTransactionDeleted = (id) => {
    transactions.value = transactions.value.filter((transaction) => transaction.id !== id)

    savedTransactionsToLocalStorage()
    toast.success('Transaction Deleted successfully')
  }

  const savedTransactionsToLocalStorage = () => {
    localStorage.setItem('transactions', JSON.stringify(transactions.value))
  }

  const generateUniqueId = () => {
    return Math.floor(Math.random() * 100000)
  }

  
 
</script>