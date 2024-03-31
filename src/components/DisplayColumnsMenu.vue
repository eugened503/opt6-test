<template>
  <div class="menu">
    <button
      class="menu__button"
      type="button"
      @click="emit('displayColumnsMenuToggle')"
    >
      <div class="menu__arrow"></div>
      <span>Отображение столбцов</span>
    </button>
    <div class="menu__body">
      <label for="id">
        <input
          type="checkbox"
          id="id"
          value="id"
          v-model="checkedNames[0].id"
        />
        <span>ID</span>
      </label>

      <label for="john">
        <input
          type="checkbox"
          id="action"
          value="action"
          v-model="checkedNames[0].action"
        />
        <span>Действие</span>
      </label>

      <label for="nameUnit">
        <input
          type="checkbox"
          id="nameUnit"
          value="nameUnit"
          v-model="checkedNames[0].nameUnit"
        />
        <span>Наименование единицы</span>
      </label>

      <label for="price">
        <input
          type="checkbox"
          id="price"
          value="price"
          v-model="checkedNames[0].price"
        />
        <span>Цена</span>
      </label>

      <label for="quantity">
        <input
          type="checkbox"
          id="quantity"
          value="quantity"
          v-model="checkedNames[0].quantity"
        />
        <span>Кол-во</span>
      </label>

      <label for="productName">
        <input
          type="checkbox"
          id="productName"
          value="productName"
          v-model="checkedNames[0].productName"
        />
        <span>Название товара</span>
      </label>

      <label for="total">
        <input
          type="checkbox"
          id="total"
          value="total"
          v-model="checkedNames[0].total"
        />
        <span>Итого</span>
      </label>
    </div>
  </div>
</template>

<script setup>
import { watch, toRef, reactive } from "vue";

// эмиты
const emit = defineEmits(["getCheckedNames", "displayColumnsMenuToggle"]);

// данные
const checkedNames = toRef(
  reactive([
    {
      id: true,
      action: true,
      nameUnit: true,
      price: true,
      quantity: true,
      productName: true,
      total: true,
    },
  ]),
);

// наблюдатели
watch(
  checkedNames,
  (newCheckedNames) => {
    if (newCheckedNames) {
      emit("getCheckedNames", checkedNames.value);
    }
  },
  { deep: true },
);
</script>

<style scoped lang="scss">
.menu {
  display: flex;
  flex-direction: column;
  gap: 10px;
  position: absolute;
  top: 21px;
  right: 15px;
  width: 210px;
  border-radius: 5px;
  box-shadow:
    0 0 3px 0 #000,
    inset 0 1px 2px 0 rgba(255, 255, 255, 0.5);
  background-color: #fff;
  padding: 7px 10px;
  z-index: 1;

  &__body {
    display: flex;
    flex-direction: column;
    gap: 10px;
  }

  label {
    display: flex;
    align-items: center;
    gap: 5px;

    input {
      cursor: pointer;
    }
  }

  &__button {
    display: flex;
    gap: 10px;
    align-items: center;
    padding: 7px 9px;

    span {
      display: block;
      text-align: left;
      font-size: 14px;
      color: #161616;
    }
  }

  &__arrow {
    right: 9px;
    width: 9px;
    height: 9px;
    border-top: 2px solid #000000;
    border-right: 2px solid #000000;
    transform: rotate(-135deg);
  }
}
</style>
