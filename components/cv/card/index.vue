<template>
  <div class="w-full h-[500px] border-solid border">
    <CvCardActions />
  </div>
</template>

<script lang="ts" setup>
// Посколькоку это SSR-компонент мы можем позволить себе глубокую декомпозицию без потери производительности
// Для CSR-компонентов лучше избегать такой в силу низкой производительности вьюшного дерева
</script>

<style></style>
