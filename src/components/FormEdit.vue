<template>
  <form @submit.prevent="handleSave" class="form">
    <h2>{{ formData.isActive ? 'Редактирование дела' : 'Актуализация дела' }}</h2>

    <label class="form-label">
      <p>Название дела:</p>
      <input
        type="text"
        v-model="formData.title"
        @blur="v$.title.$touch()"
      >
      <span class="error" v-if="v$.title.$error">
        {{ v$.title.$errors[0].$message }}
      </span>
    </label>

    <label class="form-label">
      <p>Описание дела:</p>
      <textarea v-model="formData.description" rows="3"></textarea>
    </label>

    <label class="form-label">
      <p>Дедлайн:</p>
      <input
        type="datetime-local"
        class="datetimepicker"
        v-model="formData.lasting"
        @blur="v$.lasting.$touch()"
      >
      <span class="error" v-if="v$.lasting.$error">
        {{ v$.lasting.$errors[0].$message }}
      </span>
    </label>

    <label class="checkbox-label">
      <input type="checkbox" v-model="formData.isActive">
      <span>{{ formData.isActive ? 'Дело актуально' : 'Сделать дело актуальным' }}</span>
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
        Актуализировать и сохранить
      </button>
    </div>
  </form>
</template>


<script setup>
import { ref, computed, onMounted } from "vue";
import { useVuelidate } from '@vuelidate/core';
import { required, helpers } from '@vuelidate/validators';

const props = defineProps({
  card: {
    type: Object
  }
})

const rules = computed(() => ({
  title: {
    required: helpers.withMessage('Название обязательно', required),
  },
  lasting: {
    validDate: helpers.withMessage(
      'Дедлайн должен быть в будущем',
      value => !value || new Date(value) > new Date()
    )
  }
}));

const formData = ref({
  id: '',
  title: '',
  description: '',
  lasting: '',
  isActive: true
});

onMounted(() => {
  formData.value = { ...props.card };
});

const v$ = useVuelidate(rules, formData);
const emit = defineEmits(["save", "close"]);


const handleSave = async() => {
  const isValid = await v$.value.$validate();

  if (!isValid) return
  emit("save", {...formData.value});
}

const activateAndSave = async () => {
  formData.value.isActive = true;
  await handleSave();
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