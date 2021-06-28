<template>
  <div>
    <BaseTable
      :table-column="tableColumn"
      :table-data="tableData"
      row-key="id"
      border
      :tree-props="{children: 'children'}"
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
      tableColumn: [
        { prop: 'name', label: '姓名', edit: false, disable: false, editType: 'input' },
        { prop: 'age', label: '年龄', edit: false, disable: false },
        { prop: 'sex', label: '性别', edit: false, editType: 'select', list: sexList, listKeys: sexListKeys  },
        { isSlot: true, slotName: 'sex', label: '性别' },
        { isSlot: true, slotName: 'operate', label: '操作' },
      ],
      tableData: [{
        id: 1,
        name: '波仔',
        age: 18,
        sex: '1',
      }, {
        id: 2,
        name: '小花',
        age: 23,
        sex: '2',
        children: [{
          id: 21,
          name: '小花的仔',
          age: 2,
          sex: '1',
        }]
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
  filters: {
    ageFilter(cur) {
      const obj = {
        '1': '男',
        '2': '女'
      }
      return obj[cur] || '无性别'
    }
  }
}
</script>
