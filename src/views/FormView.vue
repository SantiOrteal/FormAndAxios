<script setup>
import FormComponent from '../components/FormComponent.vue'
</script>

<template>
  <div class="about">
    <FormComponent />
  </div>
</template>

<style>
@media (min-width: 1024px) {
  .about {
    min-height: 100vh;
    display: flex;
    align-items: center;
  }
}
</style>
