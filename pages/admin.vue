<script setup lang="ts">
type keyComponent = 'type' | 'label' | 'component' | 'props'
interface IComponent {
  type: string
  label: string
  component: string
  props?: any
}
//data
const componentList = [
  {
    type: 'elementParagraph',
    label: 'Paragraph',
    component: 'Paragraph'
  },
  {
    type: 'elementButton',
    label: 'Button',
    component: 'Button'
  }
]
const components = ref<Array<IComponent>>([])
const componentActive = ref<IComponent | null>()
const componentDragging = ref()
const mousePosition = ref<String | null>()
//end data

//method
const dragStart = (event: DragEvent, data: IComponent) => {
  if (!data) return
  Object.keys(data).map((el: string) => {
    event.dataTransfer?.setData(el, data[el as keyComponent])
  })
  componentDragging.value = data
}
const drop = (event: DragEvent, index: number) => {
  console.log('drop')
}
const addComponent = (event: DragEvent) => {
  const type = event.dataTransfer?.getData('type')
  const label = event.dataTransfer?.getData('label')
  const component = event.dataTransfer?.getData('component')
  const componentResult = {
    type,
    label,
    component,
    props: {}
  }
  components?.value.push(componentResult as IComponent)
  componentDragging.value = null
}
//end method

onMounted(() => {
  document.onmousemove = handleMouseMove
  function handleMouseMove(event: MouseEvent) {
    event = event || window.event
    mousePosition.value = `(${event.pageX}, ${event.pageY})`
  }
})
</script>

<template>
  <div class="admin-page">
    <h1>Admin page</h1>
    <div class="control">
      <button>Save</button>
      <button>Undo</button>
      <button>Redo</button>
      <button>View</button>
    </div>
    <div class="component-builder">
      <div class="component-list">
        <div
          v-for="(component, index) in componentList"
          :draggable="true"
          :key="index"
          @dragstart="dragStart($event, component)"
          @dragover.prevent
          class="component">
          <div class="example"></div>
          {{ component.label }}
        </div>
      </div>
      <div class="component-attribute" @dragover.prevent @drop="addComponent($event)">
        <p>Mouse: {{ mousePosition }}</p>
        <p>Dragging: {{ componentDragging?.label }}</p>
        <p>Instance:{{ components?.length }}</p>
        <p>Config:{{ componentActive }}</p>
      </div>

      <div class="component-view">
        <component
          v-for="(component, index) in components"
          :key="index"
          :is="resolveComponent(component.component)"
          v-bind="component.props"
          @click="componentActive = component">
          {{ component?.props?.text ? component?.props.text : component.label }}</component
        >
      </div>
    </div>
    <div class="component-form">
      <div class="container-item" v-if="componentActive?.type == 'elementButton'">
        <label for="">Button Text</label>
        <input type="text" v-model="componentActive.props.text" />
        <label for="">Alert Message</label>
        <input type="text" v-model="componentActive.props.message" />
      </div>
      <div class="container-item" v-if="componentActive?.type == 'elementParagraph'">
        <label for="">Paragraph Text</label>
        <input type="text" v-model="componentActive.props.text" />
      </div>
    </div>
  </div>
</template>

<style scoped>
.admin-page {
  display: block;
}

.control {
  margin: 20px 0;
  display: flex;
  flex-direction: row;
  justify-content: center;
  gap: 10px;
  padding: 8px;
}

.component-builder {
  display: flex;
  flex-direction: row;
  width: 100%;
  border: 1px solid #ccc;
}

.component-list {
  width: 200px;
  padding: 10px;
  border-right: 1px solid #ccc;
}

.component-list > .component {
  margin-bottom: 5px;
  padding: 5px;
  cursor: move;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.component-list > .component:hover {
  background-color: #fafafa;
  overflow: 0.3;
}

.example {
  width: 80px;
  height: 80px;
  border: 1px solid #ccc;
  margin-bottom: 10px;
}

.component-attribute {
  padding: 10px 8px;
  display: flex;
  flex-direction: column;
  flex: 1;
}
.component-view {
  display: flex;
  flex-direction: column;
  gap: 10px;
  flex: 1;
  padding: 10px;
  max-width: 500px;
}
.component-form {
  border: 1px solid #ccc;
  border-top: none;
  padding: 20px;
}
.container-item {
  display: flex;
  flex-direction: column;
  gap: 4px;
}
.container-item input {
  min-width: 300px;
  width: fit-content;
}
</style>
