<template>
  <div class="menu">
    <button
      class="menu__button"
      :class="{ active: displayingColumns }"
      type="button"
      @click="emit('displayColumnsMenuToggle')"
    >
      <div class="menu__arrow"></div>
      <span>Отображение столбцов</span>
    </button>
    <div class="menu__body">
      <label for="id">
        <input type="checkbox" id="id" value="id" v-model="checkedNames.id" />
        <span>ID</span>
      </label>

      <label for="john">
        <input
          type="checkbox"
          id="action"
          value="action"
          v-model="checkedNames.action"
        />
        <span>Действие</span>
      </label>

      <label for="nameUnit">
        <input
          type="checkbox"
          id="nameUnit"
          value="nameUnit"
          v-model="checkedNames.nameUnit"
        />
        <span>Наименование единицы</span>
      </label>

      <label for="price">
        <input
          type="checkbox"
          id="price"
          value="price"
          v-model="checkedNames.price"
        />
        <span>Цена</span>
      </label>

      <label for="quantity">
        <input
          type="checkbox"
          id="quantity"
          value="quantity"
          v-model="checkedNames.quantity"
        />
        <span>Кол-во</span>
      </label>

      <label for="productName">
        <input
          type="checkbox"
          id="productName"
          value="productName"
          v-model="checkedNames.productName"
        />
        <span>Название товара</span>
      </label>

      <label for="total">
        <input
          type="checkbox"
          id="total"
          value="total"
          v-model="checkedNames.total"
        />
        <span>Итого</span>
      </label>
    </div>
  </div>
</template>

<script setup>
import { watch, toRef, reactive } from "vue";
const emit = defineEmits(["getCheckedNames", "displayColumnsMenuToggle"]);

const checkedNames = toRef(
  reactive({
    id: true,
    action: true,
    nameUnit: true,
    price: true,
    quantity: true,
    productName: true,
    total: true,
  }),
);

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
