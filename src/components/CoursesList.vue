<template>
  <h1>To-Do List</h1>
  <div class="cards-container">
  <AppCard
    v-for="(item, index) in sortedCards"
    :key="item.id"
    :card="item"
    :index="index"
    @edit="$emit('edit', item)"
    @delete="$emit('delete', item.id)"
  />
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

defineEmits(["edit", "delete"]);
const sortedCards = computed(() => [...props.cards].sort((a, b) => b.isActive - a.isActive));
const currentTime = ref(Date.now());

onMounted(() => {
  const interval = setInterval(() => {
    currentTime.value = Date.now();
  }, 60000);

  onUnmounted(() => clearInterval(interval));
});
</script>



<style scoped>

</style>