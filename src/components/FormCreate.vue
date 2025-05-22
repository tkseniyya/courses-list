<template>
  <form @submit.prevent="handleSubmit" class="form">
    <h2>Создание нового дела</h2>
    <label class="form-label">
      <p>Название:</p>
      <input
        type="text"
        v-model="newCard.title"
        @blur="v$.title.$touch()"
      >
      <span class="error" v-if="v$.title.$error">
        {{ v$.title.$errors[0].$message }}
      </span>
    </label>
    <label class="form-label">
      <p>Описание:</p>
      <textarea type="text" v-model="newCard.description"></textarea>
    </label>
    <label class="form-label">
      <p>Дедлайн:</p>
      <input
        type="datetime-local"
        class="datetimepicker"
        v-model="newCard.lasting"
        @blur="v$.lasting.$touch()"
      >
      <span class="error" v-if="v$.lasting.$error">
        {{ v$.lasting.$errors[0].$message }}
      </span>
    </label>
    <div class="form-actions">
      <button type="submit">Сохранить</button>
      <button type="button" @click="clearForm">Очистить</button>
    </div>
  </form>
</template>



<script setup>
import { ref, computed } from "vue";
import { useVuelidate } from '@vuelidate/core';
import { required, helpers } from '@vuelidate/validators';


const newCard = ref({
  id: Date.now(),
  title: '',
  description: '',
  lasting: '',
  isActive: true
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
}))

const emit = defineEmits(["success", "close"]);
const v$ = useVuelidate(rules, newCard);

const handleSubmit = async () => {
  const isValid = await v$.value.$validate();

  if (!isValid) return

  emit("success", {
    ...newCard.value,
    id: Date.now()
  });
  emit("close");
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
  v$.value.$reset()
}
</script>



<style scoped>

</style>