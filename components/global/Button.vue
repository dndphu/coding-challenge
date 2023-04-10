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
  <div class="button-element">
    <button @click="message&& $route.name !=='admin' && onClick()">
      {{ text }}
    </button>
  </div>
</template>

<style scoped>
.button-element button{
  cursor: pointer;
}
</style>
