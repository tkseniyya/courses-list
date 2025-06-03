<template>
  <div class="courses-list">
    <div class="list-header">
      <h2>{{ activeFilter }}</h2>
      <span class="tasks-count">{{ cards.length }} задач</span>
    </div>
    <div class="cards-container">
      <AppCard
        v-for="(item, index) in sortedCards"
        :key="item.id"
        :card="item"
        :index="index"
        @edit="$emit('edit', item)"
        @delete="$emit('delete', item.id)"
        @fix="$emit('fix', item.id)"
        @done="$emit('done', item.id)"
      />
    </div>
  </div>
</template>

<script setup>
import AppCard from "@/components/AppCard.vue";
import { ref, onMounted, onUnmounted, computed } from "vue";
const props = defineProps({
  cards: {
    type: Array
  }
});

defineEmits(["edit", "delete", "fix", "done"]);
const sortedCards = computed(() => {
  return [...props.cards].sort((a, b) => {
    if (a.isFixed !== b.isFixed) return a.isFixed ? -1 : 1;
    return b.isActive - a.isActive;
  });
});
const currentTime = ref(Date.now());

onMounted(() => {
  const interval = setInterval(() => {
    currentTime.value = Date.now();
  }, 60000);

  onUnmounted(() => clearInterval(interval));
});
</script>
