<template>
  <div class="app-container">
    <Sidebar>
      :active-filter="currentFilter"
      @filter-changed="handleFilterChange"
    </Sidebar>

    <div class="main-content">
      <div class="header__btn">
        <button @click="isPopUpOpen = true">Добавить дело</button>
        <button @click="deleteAll">Удалить все записи</button>
      </div>

      <CoursesList
        :cards="filteredCards"
        @edit="handleEditCard"
        @delete="handleDeleteRequest"
        @fix="toggleFixCard"
        @done="isCardDone"
      />

      <PopUp :isOpen="isPopUpOpen" @close="isPopUpOpen = false">
        <FormCreate
          @success="handleFormSuccess"
          @close="isPopUpOpen = false"
        />
      </PopUp>

      <PopUp :isOpen="isEditPopUpOpen" @close="isEditPopUpOpen = false">
        <FormEdit
          :card="editingCard"
          @save="handleSaveCard"
          @close="isEditPopUpOpen = false"
        />
      </PopUp>
      <PopUp :isOpen="isDeletePopUpOpen" @close="isDeletePopUpOpen = false">
        <FormDelete
          :card="deletingCard"
          @delete="handleDeleteCard"
        />
      </PopUp>
    </div>
  </div>
</template>

<script setup>
import {ref, onMounted, watch, computed} from "vue";

import CoursesList from './components/CoursesList.vue';
import FormCreate from "@/components/FormCreate.vue";
import FormEdit from "@/components/FormEdit.vue";
import FormDelete from "@/components/FormDelete.vue";
import PopUp from "./components/PopUp.vue";
import Sidebar from "@/components/Sidebar.vue";

const isPopUpOpen = ref(false);
const isEditPopUpOpen = ref(false);
const isDeletePopUpOpen = ref(false);

const editingCard = ref(null);
const deletingCard = ref(null);

const STORAGE_KEY = "vue-courses-app-data";

const activeFilter = ref('all');
const cards = ref([]);

const filteredCards = computed(() => {
  const now = new Date();
  const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
  const tomorrow = new Date(today);
  tomorrow.setDate(tomorrow.getDate() + 1);

  return cards.value.filter(card => {
    const cardDate = card.lasting ? new Date(card.lasting) : null;

    switch (activeFilter.value) {
      case 'all':
        return true;
      case 'today':
        return cardDate && cardDate.toDateString() === today.toDateString();
      case 'planned':
        return cardDate && cardDate >= tomorrow && !card.isDone;
      case 'completed':
        return card.isDone;
      case 'overdue':
        return cardDate && cardDate < today && !card.isDone;
      default:
        return true;
    }
  });
});

onMounted(() => {
  const savedData = localStorage.getItem(STORAGE_KEY);
  if (savedData) {
    cards.value = JSON.parse(savedData);
  }
  saveToLocalStorage();
})

const saveToLocalStorage = () => {
  localStorage.setItem(STORAGE_KEY, JSON.stringify(cards.value));
}

watch(cards, () => {
  saveToLocalStorage();
}, { deep: true });

const deleteAll = () => {
  cards.value = [];
}

const handleFormSuccess = (newCard) => {
  cards.value.unshift(newCard);
  isPopUpOpen.value = false;
};

const handleEditCard = (card) => {
  editingCard.value = { ...card };
  isEditPopUpOpen.value = true;
}

const handleSaveCard = (updatedCard) => {
  const index = cards.value.findIndex(c => c.id === updatedCard.id);
  if (index !== -1) {
    cards.value[index] = updatedCard;
  }
  isEditPopUpOpen.value = false;
}
const handleDeleteCard = (cardId) => {
  cards.value = cards.value.filter(card => card.id !== cardId);
  isDeletePopUpOpen.value = false;
};

const handleDeleteRequest = (cardId) => {
  deletingCard.value = cards.value.find(card => card.id === cardId);
  isDeletePopUpOpen.value = true;
};

const toggleFixCard = (cardId) => {
  const card = cards.value.find(card => card.id === cardId);
  if (card) {
    card.isFixed = !card.isFixed;
  }
}

const isCardDone = (cardId) => {
  const card = cards.value.find(card => card.id === cardId);
  if (card) {
    card.isDone = true;
  }
}
</script>

<style scoped>
@import './assets/global.css';
</style>