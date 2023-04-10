<script setup lang="ts">
const components = ref()
useHead({
  title: 'Nuxt - Consumer'
})
if (process.client) {
  components.value = JSON.parse(sessionStorage.getItem('layoutBuilder') ?? '')
}
</script>
<template>
  <div class="consumer-page">
    <component
      v-for="(component, index) in components"
      :key="index"
      :is="resolveComponent(component.component)"
      v-bind="component.props"
    >
      {{ component?.props?.text ? component?.props.text : component.label }}
    </component>
  </div>
</template>

<style scoped>
.consumer-page {
  display: flex;
  flex-direction: column;
  gap: 10px;
  flex: 1;
  padding: 10px;
  align-items: center;
}
</style>
