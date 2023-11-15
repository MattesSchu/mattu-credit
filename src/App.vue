<script setup lang="ts">
import TheHeader from './components/TheHeader.vue'
import TheContent from './components/TheContent.vue'
</script>

<template>
  <header>
    <div class="wrapper">
      <TheHeader />
    </div>
  </header>

  <main>
    <TheContent />
  </main>
</template>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

</style>
