<template>
  <div class="header__btn">
    <button @click="isPopUpOpen = true">Добавить дело</button>
    <button @click="deleteAll">Удалить все записи</button>
  </div>

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
  <CoursesList
    :cards="cards"
    @edit="handleEditCard"
    @delete="handleDeleteRequest"
  />
</template>

<script setup>
import {ref, onMounted, watch} from "vue";

import CoursesList from './components/CoursesList.vue';
import FormCreate from "@/components/FormCreate.vue";
import FormEdit from "@/components/FormEdit.vue";
import FormDelete from "@/components/FormDelete.vue";
import PopUp from "./components/PopUp.vue";

const isPopUpOpen = ref(false);
const isEditPopUpOpen = ref(false);
const isDeletePopUpOpen = ref(false);

const editingCard = ref(null);
const deletingCard = ref(null);

const STORAGE_KEY = "vue-courses-app-data";

const cards = ref([]);

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
</script>

<style scoped>
@import './assets/global.css';
</style>
