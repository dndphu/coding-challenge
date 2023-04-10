<script setup lang="ts">
type keyComponent = 'type' | 'label' | 'component' | 'props'
interface IComponent {
  type: string
  label: string
  component: string
  props?: any
}
//data
const router = useRouter()
const componentList = [
  {
    type: 'elementParagraph',
    label: 'Paragraph',
    component: 'Paragraph',
  },
  {
    type: 'elementButton',
    label: 'Button',
    component: 'Button',
  },
]
const components = ref<Array<IComponent>>([])
const componentActive = ref<IComponent | null>()
const componentDragging = ref()
const mousePosition = ref<String | null>()

const actions = ref([]) as any
const actionIndex = ref(0)

const isShowImport = ref(false)
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
  // console.log('drop')
}
const addComponent = (event: DragEvent) => {
  const type = event.dataTransfer?.getData('type')
  const label = event.dataTransfer?.getData('label')
  const component = event.dataTransfer?.getData('component')
  const componentResult = {
    type,
    label,
    component,
    props: {},
  }
  components?.value.push(componentResult as IComponent)
  componentDragging.value = null
  addNewAction()
}
const onSave = () => {
  alert('coming soon ^^!')
}
const onView = () => {
  if (!components.value?.length) {
    alert('Nothing to view! Please add new component')
    return
  }
  if (process.client) {
    sessionStorage.setItem('layoutBuilder', JSON.stringify(components.value))
    const routeConsumer = router.resolve({ path: '/consumer' })
    window.open(routeConsumer.href)
  }
}
function addNewAction() {
  // actions.value.splice(actionIndex.value + 1, actions.value.length - actionIndex.value - 1)
  actions.value.push([...components.value])
  actionIndex.value = actions.value.length - 1
}
const undo = () => {
  if (actionIndex.value < 1) {
    components.value = []
    actions.value = []
    //clear all
    actionIndex.value = 0
    return
  } else {
    actionIndex.value--
    components.value = [...actions.value[actionIndex.value]]
    // actions.value.pop()
  }
}
const redo = () => {
  if (actionIndex.value < actions.value.length - 1) {
    actionIndex.value++
    components.value = [...actions.value[actionIndex.value]]
  }
}
const onExport = () => {
  if (!components.value?.length) {
    alert('Nothing to Export! Please add new component')
    return
  }
  const jsonString = JSON.stringify(components.value)
  const blob = new Blob([jsonString], { type: 'application/json' })
  const url = URL.createObjectURL(blob)
  const link = document.createElement('a')
  link.download = 'data.json'
  link.href = url
  link.click()
}
// const onImport = () => {}
const handleFileUpload = (e :any) => {
  const file = e.target.files[0]
  if (!file) {
    return
  }
  const reader = new FileReader()
  reader.onload = () => {
    const json: any = reader.result
    const data = JSON.parse(json)
    components.value = data
  }

  reader.readAsText(file)
}
//end method

//watch

//end watch
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
      <button @click="onSave">Save</button>
      <button :disabled="!actions.length" @click="undo">Undo</button>
      <button :disabled="!actions.length || actionIndex === actions?.length - 1" @click="redo">
        Redo
      </button>
      <button @click="onExport">Export</button>
      <button @click="isShowImport = true">Import</button>
      <button @click="onView">View</button>
    </div>
    <div v-if="isShowImport" class="container-import">
      <input type="file" @change="handleFileUpload" />
    </div>
    <div class="component-builder">
      <div class="component-list">
        <div
          v-for="(component, index) in componentList"
          :draggable="true"
          :key="index"
          @dragstart="dragStart($event, component)"
          @dragover.prevent
          class="component"
        >
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
          @click.prevent.stop="componentActive = component"
          :key="index"
          :is="resolveComponent(component.component)"
          v-bind="component.props"
        >
          {{ component?.props?.text ? component?.props.text : component.label }} {{ index }}
        </component>
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
.container-import{
  padding: 20px;
  border: 1px solid #ccc;
  border-bottom: none;
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
  align-items: center;
  cursor: pointer;
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
