<template>
  <div class="main-content">
    <h1>Проведение ТО и мелкий ремонт</h1>
    <ul class="main-content__menu">
      <li>Общее</li>
      <li>Товары</li>
      <li>Доп. расходы</li>
      <li>
        <button type="button">
          <gearWheel />
        </button>
      </li>
    </ul>
    <TheTable />
  </div>
</template>

<script setup>
import gearWheel from "@/assets/images/gearWheel.svg";
import TheTable from "@/components/TheTable.vue";
</script>

<style scoped lang="scss">
.main-content {
  width: calc(1499 / 1728 * 100%);

  &__menu {
    display: flex;
  }
}
</style>
