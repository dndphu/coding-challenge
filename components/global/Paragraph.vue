<script setup lang="ts">
interface IProps {
  text?: string
}
const props = withDefaults(defineProps<IProps>(), {
  text: 'Paragraph'
})
</script>
<template>
  <div>
    {{ text }}
  </div>
</template>

<style scoped></style>
