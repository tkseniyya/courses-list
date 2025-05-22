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
  checkInterval = setInterval(() => {
    currentTime.value = Date.now();
  }, 60000);

  const now = new Date();
  const secondsToNextMinute = 60 - now.getSeconds();
  setTimeout(() => {
    currentTime.value = Date.now();
  }, secondsToNextMinute * 1000);
};

onMounted(() => {
  startChecking()
})

onUnmounted(() => {
  clearInterval(checkInterval);
});


</script>


<style scoped>
.card {
  background: white;
  border-radius: 8px;
  padding: 16px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
  position: relative;
  transition: all 0.3s ease;
}

.card.inactive {
  background: #f8f9fa;
}

.card.overdue {
  border-left: 4px solid #f72585;
  background-color: #fff0f0;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 10px;
}

.card-title {
  margin: 0;
  font-size: 1.2rem;
  color: #333;
}

.card-actions {
  display: flex;
  gap: 8px;
}

.action-btn {
  background: none;
  border: none;
  cursor: pointer;
  font-size: 1rem;
  padding: 5px;
  transition: all 0.2s ease;
}

.edit-btn {
  color: #666;
}

.edit-btn:hover {
  color: #4361ee;
  transform: scale(1.1);
}

.delete-btn {
  color: #666;
}

.delete-btn:hover {
  color: #f72585;
  transform: scale(1.1);
}

.inactive-message {
  color: #666;
  font-style: italic;
}

.card-description {
  margin: 8px 0;
  color: #555;
}

.card-lasting {
  display: flex;
  flex-direction: column;
  color: #4361ee;
  font-weight: 500;
}
.card-lasting span {
  color: #f72585;
  font-weight: bold;
}
</style>