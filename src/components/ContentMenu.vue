<template>
  <div class="menu">
    <button
      class="menu__button"
      :class="{ active: displayingColumns }"
      type="button"
      @click="displayingColumnsToggle"
    >
      <span>Отображение столбцов</span>
      <div class="menu__arrow"></div>
    </button>
    <button
      class="menu__button"
      :class="{ active: columnOrder }"
      type="button"
      @click="columnOrderToggle"
    >
      <span>Порядок столбцов</span>
      <div class="menu__arrow"></div>
    </button>
  </div>
</template>

<script setup>
import { ref } from "vue";

// эмиты
const emit = defineEmits(["displayColumnsMenuToggle"]);

// данные
const displayingColumns = ref(false);
const columnOrder = ref(false);

// методы
const displayingColumnsToggle = () => {
  displayingColumns.value = true;
  columnOrder.value = false;
  emit("displayColumnsMenuToggle");
};

const columnOrderToggle = () => {
  columnOrder.value = true;
  displayingColumns.value = false;
};
</script>

<style scoped lang="scss">
.menu {
  display: flex;
  flex-direction: column;
  position: absolute;
  top: 21px;
  right: 15px;
  width: 179px;
  border-radius: 5px;
  box-shadow:
    0 0 3px 0 #000,
    inset 0 1px 2px 0 rgba(255, 255, 255, 0.5);
  background-color: #fff;

  &__button {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 7px 9px;

    &:nth-child(1) {
      border-radius: 5px 5px 0 0;
    }

    &:nth-child(2) {
      border-radius: 0 0 5px 5px;
    }

    span {
      display: block;
      text-align: left;
      font-size: 14px;
      color: #161616;
    }

    &.active {
      background-color: #eef3f8;
    }
  }

  &__arrow {
    right: 9px;
    width: 9px;
    height: 9px;
    border-top: 2px solid #000000;
    border-right: 2px solid #000000;
    transform: rotate(45deg);
  }
}
</style>
