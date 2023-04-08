<script setup lang="ts">
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
const components = ref([]) as any

//method
const dragStart = (event: DragEvent, index: number, data: any) => {
  Object.keys(data).map((el) => {
    event.dataTransfer?.setData(el, data[el])
  })
}
const drop = (event:DragEvent, index: number)=>{
  console.log('drop');
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
  components.value.push(componentResult)
}
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
        <div v-for="(component, index) in componentList" :draggable="true" :key="index"
          @dragstart="dragStart($event, index, component)" @dragover.prevent class="component">
          <div class="example"></div>
          {{ component.label }}
        </div>
      </div>
      <div
        class="component-attribute"
        @dragover.prevent
        @drop="addComponent($event)"
      >
        <p>Mouse:</p>
        <p>Dragging:</p>
        <p>Instance:</p>
        <p>Config:</p>
      </div>

      <div class="component-view">
        <div v-for="(component, index) in components" :key="index">
          {{ component.label }}
        </div>
        <!-- handle show component -->
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

.component-list>.component {
  margin-bottom: 5px;
  padding: 5px;
  cursor: move;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.component-list>.component:hover {
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
  flex: 1
}
.component-view{
  display: flex;
  flex-direction: column;
  gap:10px;
  flex:1;
  padding: 10px;
  max-width: 500px
}
</style>
