<template>
  <div class="selection-box">
    <draggable v-model="items" @end="onMove" item-key="id" class="draggable-list">
      <template #item="{ element }">
        <div class="item" >
          {{ element.name }}
        </div>
      </template>
    </draggable>
  </div>
</template>

<script lang="ts">
import { ref, onMounted, toRaw } from "vue"
import { Streamlit } from "streamlit-component-lib"
import { useStreamlit } from "./streamlit"
import draggable from 'vuedraggable'

interface Item {
  id: number
  name: string
}

export default {
  name: "StreamlitSortables",
  components: {
    draggable
  },
  props: ["args"], // Arguments that are passed to the plugin in Python are accessible in prop "args"
  setup(props: any) {
    useStreamlit() // lifecycle hooks for automatic Streamlit resize
    onMounted(()=>{
      console.log("passed items: " + props.args.items)
    })

    const items = ref<Item[]>(
      props.args.items.map((name: string, index: number) => ({id: index+1, name: name}))
    )
    const itemNames = ref<string[]>([]);

    const onMove = () => {
      itemNames.value = items.value.map(item => item.name)
      const res = JSON.stringify(itemNames.value)
      Streamlit.setComponentValue(res)
    }

    return {
      items,
      onMove,
    }
  },
}
</script>

<style scoped>
.selection-box {
  padding: 10px;
  background-color: #f7f7f7;
}

.draggable-list {
  display: flex;
  flex-wrap: wrap;
  gap: 10px; /* Adjust the gap between items */
}

.item {
  padding: 10px;
  background-color: #fff;
  border: 1px solid #ccc;
  border-radius: 5px;
  cursor: grab;
  min-width: 30px;
  box-sizing: border-box;
}
.item:active {
  cursor: grabbing; /* Change cursor when item is being dragged */
}

</style>
