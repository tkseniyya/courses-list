<template>
  <h1>Courses List</h1>
  <div class = "card-container">
    <div v-for="item in sortedCards" :key="item.id">
      <div v-if="item.isActive" class="card">
        <div class="card-title">{{ item.title }}</div>
        <div class="card-description">{{ item.description }}</div>
        <div class="card-lasting">{{ item.lasting }}</div>
      </div>
      <div v-else class="card inactive">
        Курс "{{ item.title }}" уже не существует...
      </div>
    </div>
  </div>
</template>

<script setup>
import {ref, computed} from 'vue';
const cards = ref([
  {
    id: 1,
    title: 'Python',
    description: 'Этот курс посвящен изучению Python',
    lasting: '6 месяцев',
    isActive: true
  },
  {
    id: 2,
    title: 'HTML',
    description: 'Этот курс посвящен изучению HTML',
    lasting: '8 месяцев',
    isActive: false
  },
  {
    id: 3,
    title: 'CSS',
    description: 'Этот курс посвящен изучению CSS',
    lasting: '9 месяцев',
    isActive: true
  },
]);
const sortedCards = computed(() => {
  return [...cards.value].sort((a, b) => {
    return b.isActive - a.isActive;
  });
});
</script>



<style scoped>

.card {
  background-color: #ffe4a2;
  color: black;
  border: 1px solid #ccc;
  padding: 1rem;
  margin: 1rem 0;
  border-radius: 20px;
  width: 300px ;
}

.card.inactive {
  background-color: #ffaa9b;
  color: #ff4b46;
}
</style>