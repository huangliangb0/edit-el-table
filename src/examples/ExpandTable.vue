<template>
  <div>
    <el-row class="action-box" style="margin-bottom: 20px;">
      <el-button @click="toggle">普通表格/扩展表格 切换</el-button>
    </el-row>
    <BaseTable
      :table-column="tableColumn"
      :table-data="tableData"
      :show-index="false"
    > 
      <!-- 具名插槽 -->
      <template #sex="{$index, row}">
        {{$index}} : {{ row.sex | ageFilter }}
      </template>
      <!-- 默认插槽 -->
      <template #operate="{ row }">
        <el-button type="primary" size="mini">编辑</el-button>
        <el-button size="mini">完成</el-button>
      </template>
      <template v-slot="{ row }">
        <div>
          <p>展开内容</p>
          <p>姓名：{{ row.name }}</p>
          <p>年龄：{{ row.age }}</p>
          <p>性别: {{ row.sex | ageFilter }}</p>
        </div>
      </template>
    </BaseTable>
  </div>
</template>

<script>
import BaseTable from '../components/BaseTable/index.vue'
const sexList = [
 { id: '1', name: '男' },
 { id: '2', name: '女' },
]
const sexListKeys = {
  label: 'name',
  value: 'id'
}
export default {
  name: 'App',
  components: {
    BaseTable
  },
  data() {
    return {
      isExpand: true,
      tableData: [{
        id: 1,
        name: '波仔',
        age: 18,
        sex: '1'
      }, {
        id: 2,
        name: '小花',
        age: 19,
        sex: '2'
      }, {
        id: 3,
        name: '小玲',
        age: 20,
        sex: '2'
      }, , {
        id: 4,
        name: '张三',
        age: 20,
        sex: '1'
      }]
    }
  },
  computed: {
    tableColumn() {
      const tableColumn = [
        { type: 'index', label: '序号', width: 50 },
        { type: 'selection', width: 50 },
        { prop: 'name', label: '姓名', edit: false, disable: false, editType: 'input' },
        { prop: 'age', label: '年龄', edit: false, disable: false },
        { prop: 'sex', label: '性别', edit: false, editType: 'select', list: sexList, listKeys: sexListKeys  },
        { isSlot: true, slotName: 'sex', label: '性别' },
        { isSlot: true, slotName: 'operate', label: '操作' },
      ]

      if (this.isExpand) {
        return [...tableColumn, { isSlot: true,  type: 'expand', width: '20' }] 
      }

      return tableColumn
    }
  },
  filters: {
    ageFilter(cur) {
      const obj = {
        '1': '男',
        '2': '女'
      }
      return obj[cur] || '无性别'
    }
  },
  methods: {
    toggle() {
      this.isExpand = !this.isExpand
    }
    
  }
  
}
</script>
