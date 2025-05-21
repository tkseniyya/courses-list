<template>
  <form @submit.prevent="handleSave" class="form">
    <h2>{{ formData.isActive ? 'Редактирование курса' : 'Активация курса' }}</h2>

    <label>
      <p>Название курса:</p>
      <input type="text" v-model="formData.title" required>
    </label>

    <label>
      <p>Описание курса:</p>
      <textarea v-model="formData.description" rows="3"></textarea>
    </label>

    <label>
      <p>Длительность курса:</p>
      <input type="text" v-model="formData.lasting">
    </label>

    <label class="checkbox-label">
      <input type="checkbox" v-model="formData.isActive">
      <span>{{ formData.isActive ? 'Активный курс' : 'Сделать курс активным' }}</span>
    </label>

    <div class="form-actions">
      <button type="submit" class="button-primary">Сохранить</button>
      <button type="button" class="button-outline" @click="$emit('close')">Отмена</button>
      <button
        v-if="!formData.isActive"
        type="button"
        class="button-success"
        @click="activateAndSave"
      >
        Активировать и сохранить
      </button>
    </div>
  </form>
</template>


<script setup>
import { ref } from "vue";

const props = defineProps({
  card: {
    type: Object
  }
})

const emit = defineEmits(["save", "close"]);
const formData = ref({...props.card})

const handleSave = () => {
  emit("save", {...formData.value});
}

const activateAndSave = () => {
  formData.value.isActive = true;
  handleSave();
}
</script>



<style scoped>
.form {
  max-width: 500px;
  margin: 0 auto;
}

textarea {
  min-width: 100%;
  min-height: 100px;
  max-height: 300px;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  resize: vertical;
}

.checkbox-label {
  display: flex;
  align-items: center;
  gap: 8px;
  margin: 1rem 0;
}

.form-actions {
  display: flex;
  gap: 10px;
  margin-top: 20px;
  flex-wrap: wrap;
}

.button-primary {
  background-color: #4361ee;
  color: white;
}

.button-outline {
  background: transparent;
  border: 1px solid #4361ee;
  color: #4361ee;
}

.button-success {
  background-color: #4cc9f0;
  color: white;
}
</style>