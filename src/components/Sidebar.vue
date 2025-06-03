<template>
  <div class="sidebar">
    <div class="sidebar-section">
      <ul>
        <li
          v-for="filter in filters"
          :key="filter.value"
          @click="setActiveFilter(filter.value)"
          :class="{ active: activeFilter === filter.value }"
        >
          {{ filter.label }}
        </li>
      </ul>
    </div>
    <div class="sidebar-section">
      <div class="section-header">
        <h3>Мои списки</h3>
        <span>Добавить</span>
      </div>
      <ul>
        <li
          v-for="list in lists"
          :key="list"
          @click="updateActiveList(list)"
          :class="{ active: activeList === list }"
        >
          {{ list }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
const props = defineProps({
  activeFilter: {
    type: String,
    default: 'all'
  },
  lists: {
    type: Array,
    default: () => []
  },
  activeList: {
    type: String,
    default: 'Все'
  }
});

const emit = defineEmits(['update:filter', 'update:list']);

const filters = [
  { value: 'all', label: 'Все' },
  { value: 'today', label: 'Сегодня' },
  { value: 'planned', label: 'В планах' },
  { value: 'completed', label: 'Завершено' },
  { value: 'overdue', label: 'Просроченные' }
];

const setActiveFilter = (filter) => {
  emit('update:filter', filter);
};
</script>