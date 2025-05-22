<template>
  <div :class="['card', { inactive: !card.isActive, overdue: isOverdue && card.isActive }]">
    <div class="card-header">
      <h3 class="card-title">{{ card.title }}</h3>
      <div class="card-actions">
        <button @click.stop="$emit('edit', card)" class="action-btn edit-btn" title="Редактировать">
          ✏️
        </button>
        <button @click.stop="$emit('delete', card.id)" class="action-btn delete-btn" title="Удалить">
          🗑️
        </button>
      </div>
    </div>

    <div v-if="card.isActive">
      <div class="card-description">{{ card.description }}</div>
      <div class="card-lasting">
        {{ formatDate(card.lasting) }}
        <span v-if="isOverdue" class="overdue-badge">Дедлайн просрочен</span>
      </div>
    </div>
    <div v-else class="inactive-message">
      Дело неактуально
    </div>
  </div>
</template>


<script setup>
import { ref, computed, onMounted, onUnmounted } from "vue";

const props = defineProps({
  index: Number,
  card: Object
})
defineEmits(['edit', 'delete']);

const currentTime = ref(Date.now());

const isOverdue = computed(() => {
  if (!props.card.lasting || !props.card.isActive) return false;
  const deadline = new Date(props.card.lasting);
  return currentTime.value > deadline;
})

const formatDate = (datetimeStr) => {
  if (!datetimeStr) return;
  const date = new Date(datetimeStr);
  return date.toLocaleString("ru-RU", {
    day: "numeric",
    month: "long",
    year: "numeric",
    hour: "2-digit",
    minute: "2-digit",
  });
};

let checkInterval;

const startChecking = () => {
  currentTime.value = Date.now();
  checkInterval = setInterval(() => {
    currentTime.value = Date.now();
  }, 1000);
};
onMounted(() => {
  startChecking()
})

onUnmounted(() => {
  clearInterval(checkInterval);
});
</script>

<style scoped>
</style>