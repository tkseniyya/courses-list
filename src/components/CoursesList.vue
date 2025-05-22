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
import { computed } from "vue";
const props = defineProps({
  cards: {
    type: Array
  }
});

defineEmits(["edit", "delete"]);
const sortedCards = computed(() => [...props.cards].sort((a, b) => b.isActive - a.isActive));
</script>



<style scoped>
.cards-container {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 20px;
  margin-top: 20px;
}
</style>