<template> 
  <div    :class="colClassName"  @click.native.stop="conf.mode === 'designer'&&activeFormItem&&activeFormItem(item)">
    <template v-if="conf.mode==='designer'">
      <span class="drawing-item-delete" @click="deleteItem(index, parent)" title="删除">
        <el-icon><ele-Delete /></el-icon>
      </span>
      <span class="drawing-item-copy" @click="copyItem(item, parent)" title="复制">
        <el-icon><ele-CopyDocument /></el-icon>
      </span>
    </template>
    <my-card v-if="item.tag === 'el-card'" :active-id="activeId" :item="item" :conf="conf"></my-card>
    <my-tabs v-else-if="item.tag === 'el-tabs'" :active-id="activeId" :item="item" :conf="conf"></my-tabs>
    <MyDataTable v-else-if="item.tag === 'ai-data-table'" :active-id="activeId" :item="item" :conf="conf"></MyDataTable>
    
    <MyTableLayout v-else-if="item.tag === 'table-layout'" :active-id="activeId" :item="item" :conf="conf"></MyTableLayout>
    <MyInputTable v-else-if="item.tag === 'fc-input-table'" :active-id="activeId" :item="item" :conf="conf"></MyInputTable>
    <my-row v-else-if="item.tag === 'el-row'" :active-id="activeId" :item="item" :conf="conf"></my-row>
    <render-form-item v-else :active-id="activeId" :item="item" :conf="conf"></render-form-item>
  </div> 
</template>
  
  <script lang="ts" setup name="drag-item">
import { defineAsyncComponent, computed, inject, provide } from 'vue'

const RenderFormItem = defineAsyncComponent(() => import('./RenderFormItem.vue'))
const MyRow = defineAsyncComponent(() => import('./Elements/MyRow.vue'))
const MyCard = defineAsyncComponent(() => import('./Elements/MyCard.vue'))
const MyDataTable = defineAsyncComponent(() => import('./Elements/MyDataTable.vue'))
const MyTabs = defineAsyncComponent(() => import('./Elements/MyTabs.vue'))
const MyTableLayout = defineAsyncComponent(() => import('./Elements/MyTableLayout.vue'))
const MyInputTable = defineAsyncComponent(() => import('./Elements/MyTable.vue'))

const props = defineProps({
  parent: Object,
  activeId: String || Number,
  item: Object,
  conf: Object,
  index: String || Number,
})

const activeFormItem = inject('activeFormItem')
provide('activeFormItem', activeFormItem)
const copyItem = inject('copyItem')
const deleteItem = inject('deleteItem')
 

let colClassName = computed(() => {
  if(props.conf.mode === 'designer')
  {
  return props.conf.activeId === props.item.formId ? 'drawing-item active-form-item' : 'drawing-item '
  }
  return ''
})
</script>