<script setup lang="ts">
type Props = {
  appName: string;
};
defineProps<Props>();
</script>

<template>
  <header class="bg-blue-200">
    <nav
      class="mx-auto flex max-w-7xl items-center justify-between p-6 lg:px-8"
      aria-label="Global"
    >
      <div class="flex lg:flex-1">
        <p>{{ appName }}</p>
      </div>
    </nav>
  </header>
</template>

<style scoped></style>
