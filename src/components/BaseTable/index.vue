<!--
 * @Author: 黄良波
 * @Email  864181495@qq.com
 * @Descripttion: 自定义封装el-table，其属性和事件完全继承el-table，你可以根据自己的需求，灵活更改其内容
-->
<!--
  基于el-table的再次封装

    # table的prop参数说明
      showIndex:     是否显示索引, 默认 true
      loading:       是否显示加载, 默认 false
      showSelect:    是否显示多选框，默认false
      showRadio:     是否显示单选框，默认false
      radioKey：     单选框唯一标识，通过该标识来判断radio是否选中状态，默认值ID
      showPage:      是否显示页码， 默认 true
      align          left right center  文本对齐方式

      ### 注意：showIndex， showSelect 可以通过 el-column 的 type属性配置得到，索引这两个属性，非必须

      其它属性和事件，完全继承了el-table，可参考其文档填写

    ### 表头，表头的字段继承了el-column，有一些新增的字段
      tableColumn： <Array>{
        // ### 原有属性
        prop: 'date',         // 显示字段
        label: '日期',        // 字段名
        width: '180',        // 列的宽度
        fixed: 'left',       // 是否固定位置 true left right,值为空或者不存在就不会固定
        type: 'index'        // selection/index/expand
        //  其它属性
        [string]: any // 继承el-table-column的所有属性

        // ### 以下是新增的字段
        isSlot: Boolean,        // 是否插槽显示，通常用来放一些操作按钮
        slotName: String     // 具名操作，如果每一行有多个插槽的方式，那具名插槽将很诱人
        edit: Boolean,        // 该列是否可编辑, true 可编辑
        editType: 'input'   // input：输入框； select：选择框，目前只支持这两种格式，可以更具自己的需求进行拓展
        list: <Array>{      // 如果输入方式是选择框，需要一个选择列表值
          label: string | number
          value: string | number
        },
        listKeys: Null | { // 和list一直使用，指明label和value，一般label对应name，value对应id
          label: string,
          value: string
        }
      tooltip: Boolean // 为true时，当内容过长，会出现省略号，鼠标引入会显示所有其内容
      }

    ### 表格数据列表
      tableData: <number | string>[]

    ### 新增监听的 method 方法
      pageSizeChange        切换页面显示条的大小触发
      currentChange     切换当前页触发
      radioChange           单选

  // 其它方法和属性完全继承 el-el-table
-->
<template>
  <div class="el-table--main">
    <el-table
      v-loading="loading"
      class="my-el-table"
      :data="tableData"
      element-loading-background="rgba(55,55,55,0.4)"
      element-loading-text="数据正在加载中"
      element-loading-spinner="el-icon-loading"
      style="width: 100%;"
      v-bind="$attrs"
      v-on="$listeners"
    >
      <!-- 单选框 -->
      <el-table-column
        v-if="showRadio"
        label="选项"
        width="55"
      >
        <template v-slot="{$index, row}">
          <span>
            <input 
              class="el-table--radio"
              :class="row[radioKey] === radioId ? 'checked' : ''"
              type="radio"
              name="my--radio"
              :checked="row[radioKey] === radioId"
              @change="handleRadioChange($index, row)"
            >
            <label :for="'el-table-radio-' + row[radioKey]"></label>
          </span>
        </template>
      </el-table-column>

      <!-- 是否显示索引 -->
      <el-table-column
        v-if="showIndex"
        label="序号"
        type="index"
        width="64px"
        align="center"
        :index="indexMethod"
      />

      <!-- 多选框 -->
      <TableColumn
        v-if="showSelect"
        type="selection"
        width="55"
      />

      <template v-for="(col,index) in _tableColumn">
        <!-- 
          type 为 expand 存在插槽情况
          所以expand必须配合 isSlot来使用
         -->
        <el-table-column
          v-if="col.type === 'selection' || col.type === 'index'"
          :key="index"
          v-bind="{align, ...col}"
        />
        <el-table-column
          v-else
          :key="index"
          v-bind="{align, ...col}"
        >
          <template v-slot="scope">
            <template v-if="col.isSlot">
              <template v-if="col.slotName">
                <slot v-bind="scope" :name="col.slotName" />
              </template>
              <template v-else>
                <slot v-bind="scope" />
              </template>
            </template>
            <template v-else-if="col.edit || scope.row.edit">
              <HandleInput v-model="scope.row[col.prop]" v-bind="{ ...col, disabled: !col.always && (col.disabled || scope.row.disabled) }" />
            </template>
            <template v-else>
              <el-tooltip
                v-if="col.tooltip"
                effect="light"
                :content="scope.row[col.prop]"
                placement="top-start"
              >
                <span style="white-space: nowrap;">{{ scope.row[col.prop] }}</span>
              </el-tooltip>
              <span v-else>{{ scope.row[col.prop] }}</span>
            </template>
          </template>
        </el-table-column>
      </template>
    </el-table>

    <div v-if="showPage" class="page-box">
      <Pagination
        v-show="tableData.length > 0"
        :page-size="pageOptions.pageSize"
        :total="pageOptions.total"
        :current-page="pageOptions.currentPage"
        @current-change="handleCurrentChange"
      >
      </Pagination>
    </div>
  </div>
</template>
<script>
import HandleInput from './HandleInput'
import Pagination from './Pagination'
export default {
  name: 'Baseel-table',
  components: { HandleInput, Pagination },
  props: {
    showIndex: {
      type: Boolean,
      default: true
    },
    loading: {
      type: Boolean,
      default: false
    },
    showSelect: {
      type: Boolean,
      default: false
    },
    showRadio: {
      type: Boolean,
      default: false
    },
    radioKey: {
      type: String,
      default: 'id'
    },
    showPage: {
      type: Boolean,
      default: true
    },
    align: {
      type: String,
      default: 'left'
    },
    tableData: {
      type: Array,
      default() {
        return []
      }
    },
    tableColumn: {
      type: Array,
      default() {
        return []
      }
    },
    pageOptions: {
      type: Object,
      default() {
        return {
          pageSize: 20,
          currentPage: 1,
          total: 0
        }
      }
    }
  },
  data() {
    return {
      radioId: '',
      expands: []
    }
  },
  computed: {
    _tableColumn() {
      const types = ['selection','index','expand']
      const columns = this.tableColumn
      /**
       * 表头应当存在 prop
       * 或者存在 type，并且type为selection/index/expand值
       * 或者存在插槽 isSlot。
       * 如果不满足条件，应当忽略掉
       */
      return columns.reduce((prev, next) => {
        return next.prop || types.includes(next.type) || next.isSlot
          ? [...prev, next]
          : prev
      }, [])
    },
  },
  methods: {
    indexMethod(index) {
      return this.pageOptions.pageSize * (this.pageOptions.currentPage - 1) + index + 1
    },

    // 单选
    handleRadioChange($index, row) {
      // radioKey可能是ID userid articleid这样的关键词，通过ID的比对来判断radio是否勾选
      this.radioId = row[this.radioKey]
      this.$emit('radioChange', { $index, row })
    },

    // 切换当前页
    handleCurrentChange(val) {
      this.$emit('currentChange', val)
    }
  }
}
</script>

<style lang="scss" scoped>
.el-table--radio {
  -webkit-appearance: none;
  width: 20px;
  height: 20px;
  padding: 0;
  background-color: #fff;
  border: 1px solid #c9c9c9;
  border-radius: 50%;
  outline: none;
  cursor: pointer;
  position: relative;
  &::before {
    $dis: 4px;
    display: inline-block;
    content: '';
    position: absolute;
    left: $dis;
    right: $dis;
    top: $dis;
    bottom: $dis;
    background-color: none;
    border-radius: 50%;
    transition: background-color 0.5s;
  }
  &.checked {
    &::before {
      background-color: #409EFF;
    }
  }
}
</style>