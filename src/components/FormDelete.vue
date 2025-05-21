<template>
  <form @submit.prevent="handleDelete" class="form">
    <h2>Удаление курса {{ card.title }}</h2>
    <p>Вы точно хотите удалить курс?</p>
    <div class="form-actions">
      <button type="submit" class="button-danger">Да, удалить курс</button>
      <button type="button" class="button-outline" @click="$emit('close')">Отмена</button>
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

const emit = defineEmits(["delete", "close"]);

const handleDelete = () => {
  emit("delete", props.card.id);
  emit("close");
}

</script>



<style scoped>
.button-danger {
  background-color: #f72585;
  color: white;
}
</style>