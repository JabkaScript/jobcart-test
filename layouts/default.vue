<template>
  <UContainer class="p-0 sm:p-0 lg:p-0 h-screen flex">
    <!-- Sidebar -->
    <Sidebar v-model:is-collapse="sidebarCollapsed" />

    <!-- Main Content -->
    <!-- <main 
      class="transition-all duration-300 overflow-auto p-5 h-screen w-full" 
    > -->

    <UContainer>
      <slot />
    </UContainer>
    <!-- </main> -->
  </UContainer>
</template>

<script setup lang="ts">
// Layout component for the application
const sidebarCollapsed = ref(false);
</script>
