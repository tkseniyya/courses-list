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
      <textarea
        type="text"
        v-model="formData.description"
        @blur="v$.description.$touch()"
      ></textarea>
      <span class="error" v-if="v$.description.$error">
        {{ v$.description.$errors[0].$message }}
      </span>
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
    <div class="form-checkboxes">
      <label class="checkbox-label">
        <input type="checkbox" v-model="formData.isActive">
        <span>{{ formData.isActive ? 'Дело актуально' : 'Сделать дело актуальным' }}</span>
      </label>

      <label class="checkbox-label">
        <input type="checkbox" v-model="formData.isFixed">
        <span>{{ formData.isFixed ? 'Дело закреплено' : 'Закрепить дело' }}</span>
      </label>
    </div>


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
import {required, helpers, minLength} from '@vuelidate/validators';

const props = defineProps({
  card: {
    type: Object
  }
})

const rules = computed(() => ({
  title: {
    required: helpers.withMessage('Название обязательно', required),
  },
  description: {
    minLength: helpers.withMessage('Описание должно иметь не менее 3 символов', minLength(3))
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
  isActive: true,
  isFixed: false,
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

</style>