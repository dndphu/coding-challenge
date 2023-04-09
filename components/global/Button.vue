<script setup lang="ts">
interface IProps {
  text?: string
  message?: string
}
const props = withDefaults(defineProps<IProps>(), {
  text: 'Button'
})

const onClick = () => {
  alert(props?.message)
}
</script>
<template>
  <div>
    <button @click="message && onClick">
      {{ text }}
    </button>
  </div>
</template>

<style scoped></style>
