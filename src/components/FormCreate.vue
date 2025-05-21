<template>
  <form @submit.prevent="handleSubmit" class="form">
    <h2>Создание нового курса: </h2>
    <label>
      <p>Название курса:</p>
      <input type="text" v-model="newCard.title">
    </label>
    <label>
      <p>Описание курса:</p>
      <input type="text" v-model="newCard.description">
    </label>
    <label>
      <p>Длительность курса:</p>
      <input type="text" v-model="newCard.lasting">
    </label>
    <div class="form-actions">
      <button type="submit">Сохранить</button>
      <button type="button" @click="clearForm">Очистить</button>
    </div>
  </form>
</template>



<script setup>
import { ref } from "vue";

const emit = defineEmits(["success"]);
const newCard = ref({
  id: 0,
  title: '',
  description: '',
  lasting: '',
  isActive: true
})

const handleSubmit = () => {
  if (!newCard.value.title) return;

  emit("success", {...newCard.value});
  clearForm();
}

const clearForm = () => {
  newCard.value = {
    id: 0,
    title: '',
    description: '',
    lasting: '',
    isActive: true
  };
}

</script>



<style scoped>
.form-actions {
  display: flex;
  gap: 1rem;
  margin-top: 1rem;
}
</style>