<template>
  <div class="w-full flex gap-4">
    <UButton v-for="action in actions" :key="action.icon" variant="outline">
      <UIcon :name="`i-heroicons-${action.icon}`" />
    </UButton>
  </div>
</template>

<script lang="ts" setup>
type TAction = { icon: string; title?: string; callback?: () => unknown };
const actions: TAction[] = [{ icon: "clipboard" }];
</script>

<style></style>
