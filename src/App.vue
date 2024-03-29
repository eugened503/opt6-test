<template>
  <div class="container">
    <MainSidebar />
    <MainContent />
  </div>
</template>

<script setup>
import MainContent from "@/components/MainContent.vue";
import MainSidebar from "@/components/MainSidebar.vue";
</script>

<style scoped lang="scss">
.container {
  display: flex;
  min-height: 100vh;
}
</style>
