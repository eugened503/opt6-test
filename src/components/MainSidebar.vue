<template>
  <div class="main-sidebar">MainSidebar</div>
</template>

<script setup></script>

<style scoped lang="scss">
.main-sidebar {
  width: calc(229 / 1728 * 100%);
  background-image: radial-gradient(circle at 29% 0, #000, #1c2734 103%);

  @media screen and (max-width: 767px) {
    display: none;
  }
}
</style>
