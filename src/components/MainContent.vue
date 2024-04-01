<template>
  <div
    class="main-content"
    @click="
      () => {
        isContentMenu = false;
        isDisplayColumnsMenu = false;
      }
    "
  >
    <div class="main-content__header">
      <button class="main-content__button" type="button">
        <threeLines />
      </button>
      <h1 class="main-content__title">Проведение ТО и мелкий ремонт</h1>
    </div>
    <div class="main-content__menu">
      <ul class="main-content__menu-nav">
        <li>Общее</li>
        <li class="active">Товары</li>
        <li>Доп. расходы</li>
      </ul>
      <button
        class="main-content__menu-button"
        type="button"
        @click.stop="ContentMenuToggle"
      >
        <gearWheel />
      </button>
      <ContentMenu
        v-if="isContentMenu"
        @display-columns-menu-toggle="displayColumnsMenuToggle"
      />
      <DisplayColumnsMenu
        v-if="isDisplayColumnsMenu"
        @display-columns-menu-toggle="displayColumnsMenuToggle"
        @get-checked-names="getCheckedNames"
      />
    </div>
    <TheTable :checkedNamesArr="checkedNamesArr" />
  </div>
</template>

<script setup>
import { ref, reactive } from "vue";
import gearWheel from "@/assets/images/gearWheel.svg";
import TheTable from "@/components/TheTable.vue";
import ContentMenu from "@/components/ContentMenu.vue";
import DisplayColumnsMenu from "@/components/DisplayColumnsMenu.vue";
import threeLines from "@/assets/images/threeLines.svg";

// данные
const isContentMenu = ref(false);
const isDisplayColumnsMenu = ref(false);
const checkedNamesArr = ref(reactive([]));

// методы
const getCheckedNames = (arr) => (checkedNamesArr.value = arr);

const ContentMenuToggle = () => {
  isContentMenu.value = !isContentMenu.value;
  isDisplayColumnsMenu.value = false;
};

const displayColumnsMenuToggle = () => {
  isDisplayColumnsMenu.value = !isDisplayColumnsMenu.value;
};
</script>

<style scoped lang="scss">
.main-content {
  padding: 19px 25px 25px 25px;
  width: calc(1499 / 1728 * 100%);
  background-color: #fbfcfd;

  @media screen and (max-width: 767px) {
    padding: 9px 10px 16px;
    width: 100%;
  }

  &__header {
    display: flex;
    gap: 25px;
    align-items: flex-start;
  }

  &__button {
    display: none;
    margin: 9px 0 0;
    width: 15px;
    height: 12px;

    svg {
      width: 100%;
      height: 100%;
    }

    @media screen and (max-width: 767px) {
      display: block;
    }
  }

  &__title {
    font-size: 30px;
  }

  &__menu {
    position: relative;
    display: flex;
    justify-content: space-between;
    margin: 22px 0 0;
    padding: 0 15px 0 0;

    @media screen and (max-width: 767px) {
      margin: 17px 0 0;
    }
  }

  &__menu-nav {
    display: flex;

    li {
      font-weight: 600;
      color: #1253a2;
      cursor: pointer;

      &:nth-child(2) {
        margin: 0px 28px 0 24px;

        @media screen and (max-width: 767px) {
          margin: 0 20px;
        }
      }

      &.active {
        color: #000;
      }
    }
  }

  &__menu-button {
    display: block;
    padding: 4px 1px 0 5px;
    cursor: pointer;

    @media screen and (max-width: 767px) {
      display: none;
    }
  }
}
</style>
