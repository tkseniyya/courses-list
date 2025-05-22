<template>
  <form @submit.prevent="handleDelete" class="form">
    <h2>Удаление дела "{{ card.title }}"</h2>
    <p>Вы точно хотите удалить дело?</p>
    <div class="form-actions">
      <button type="submit" class="button-danger">Да</button>
    </div>
  </form>
</template>


<script setup>
const props = defineProps({
  card: {
    type: Object
  }
})

const emit = defineEmits(["delete"]);

const handleDelete = () => {
  emit("delete", props.card.id);
}

</script>



<style scoped>

</style>