<template>
  <div class="main-content">
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
        @click="ContentMenuToggle"
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
      />
    </div>
    <!-- <TheTable /> -->
  </div>
</template>

<script setup>
import { ref } from "vue";
import gearWheel from "@/assets/images/gearWheel.svg";
//import TheTable from "@/components/TheTable.vue";
import ContentMenu from "@/components/ContentMenu.vue";
import DisplayColumnsMenu from "@/components/DisplayColumnsMenu.vue";
import threeLines from "@/assets/images/threeLines.svg";
const isContentMenu = ref(false);
const isDisplayColumnsMenu = ref(false);

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
  padding: 25px;
  width: calc(1499 / 1728 * 100%);
  background-color: #fbfcfd;

  @media screen and (max-width: 767px) {
    padding: 16px 10px;
    width: 100%;
  }

  @media screen and (max-width: 375px) {
    //margin: 0 0 16px;
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
    margin: 25px 0 0;
    padding: 0 15px 0 0;
  }

  &__menu-nav {
    display: flex;

    li {
      font-weight: 600;
      color: #1253a2;

      &:nth-child(2) {
        margin: 0 25px 0 20px;

        @media screen and (max-width: 767px) {
          margin: 0 20px 0 20px;
        }
      }

      &.active {
        color: #000;
      }
    }
  }

  &__menu-button {
    display: block;

    @media screen and (max-width: 767px) {
      display: none;
    }
  }
}
</style>
