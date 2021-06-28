<template>
  <div class="app-main">
    <BaseTabs
      :list="tabs"
      v-model="name"
    >
      <EditTable slot="edit"/>
      <ExpandTable slot="expand"/>
      <TableWithChild slot="tree"/>
    </BaseTabs>
  </div>
</template>

<script>
import BaseTabs from './components/BaseTabs/index.vue'
import EditTable from './examples/EditTable.vue'
import ExpandTable from './examples/ExpandTable.vue'
import TableWithChild from './examples/TableWithChild.vue'

export default {
  name: 'App',
  components: {
    BaseTabs,
    EditTable,
    ExpandTable,
    TableWithChild
  },
  data() {
    return {
      name: 'edit',
      tabs: [{
        name: 'edit',
        label: '可编辑表格'
      }, {
        name: 'expand',
        label: '可展开表格'
      }, {
        name: 'tree',
        label: '带有子级的树形表格'
      }]
    }
  },
}
</script>

<style>
.app-main {
  width: 1000px;
  margin: 50px auto;
}
</style>
