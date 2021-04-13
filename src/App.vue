<template>
  <div id="app">
    <el-row class="action-box" style="margin-bottom: 20px;">
      <el-button @click="toggleColClick('name')">
        {{ tableColumn[0].edit ? '完成编辑': '编辑姓名列' }}
      </el-button>
      <el-button @click="toggleColClick('age')">
        {{ tableColumn[1].edit ? '完成编辑': '编辑年龄列' }}
      </el-button>
      <el-button @click="toggleColClick('sex')">
        {{ tableColumn[2].edit ? '完成编辑': '编辑性别' }}
      </el-button>
      <el-button @click="limitRowClick(2)">
        禁止前两行输入
      </el-button>
      <el-button @click="unlockRowClick(2)">
        解除输入
      </el-button>
    </el-row>
    <EditTable
      :table-column="tableColumn"
      :table-data="tableData"
    > 
      <!-- 具名插槽 -->
      <template v-slot:sex="{$index, row}">
        {{$index}} : {{ row.sex | ageFilter }}
      </template>
      <!-- 默认插槽 -->
      <template v-slot="{ row }">
        <el-button type="primary" @click="editCurItemClick(row)" size="mini">编辑</el-button>
        <el-button @click="editCompleteClick(row)" size="mini">完成</el-button>
      </template>
    </EditTable>
  </div>
</template>

<script>
import EditTable from './components/EditTable/index.vue'
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
    EditTable
  },
  data() {
    return {
      tableColumn: [
        { prop: 'name', label: '姓名', edit: false, disable: false, editType: 'input' },
        { prop: 'age', label: '年龄', edit: false, disable: false },
        { prop: 'sex', label: '性别', edit: false, editType: 'select', list: sexList, listKeys: sexListKeys  },
        { isSlot: true, slotName: 'sex', label: '性别' },
        { isSlot: true, label: '操作' },
      ],
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
    // 编辑当前行
    editCurItemClick(row) {
      this.$set(row, 'edit', true)
    },
    // 编辑完成
    editCompleteClick(row) {
      row.edit = false
    },
    // 切换某一列能编辑
    toggleColClick(field) {
      let that = this
      this.tableColumn.some((item,index) => {
        if (item.prop === field) {
          item.edit = !item.edit
        }
      })
    },
    // 禁止前两行能编辑
    limitRowClick(count=2) {
      for(let i = 0; i < count; i++) {
        this.$set(this.tableData[i], 'disabled', true)
      }
    },
    // 解除禁止
    unlockRowClick(count=2) {
      for(let i = 0; i < count; i++) {
        this.tableData[i].disabled = false
      }
    }
  }
  
}
</script>

<style>
#app {
  width: 800px;
  margin: 50px auto;
}
</style>
