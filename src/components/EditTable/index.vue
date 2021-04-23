<!--
 * @Author: 黄良波
 * @LastEditors: 黄良波
 * @Date: 2020-09-25 09:50:48
 * @LastEditTime: 2020-10-19 20:07:49
 * @Descripttion: 自定义封装el-table
 * @FileName: Do not edit
-->
<!--
  基于el-table的再次封装
  
    el-table的prop参数说明
      showIndex:     是否显示索引, 默认 true
      loading:       是否显示加载, 默认 false
      showSelect:    是否显示多选框, 默认 false
      showRadio:     是否显示单选框，默认false
      radioKey：     单选框唯一标识，通过该标识来判断radio是否选中状态，默认值ID
      showPage:      是否显示页码， 默认 true

    // 表头，表头的字段继承了el-column，有一些新增的字段
    tableColumn： <Array>{
      prop: 'date',         // 显示字段
      label: '日期',        // 字段名
      width: '180',        // 列的宽度
      fixed: 'left',       // 是否固定位置 true left right,值为空或者不存在就不会固定
      // ### 以下是新增的字段
      isSlot: true,        // 是否插槽显示，通常用来放一些操作按钮
      slotName: '波仔'     // 如果每一行有多个插槽的方式，那具名插槽将很诱人
      edit: false,        // 该列是否可编辑
      editType: 'input'   // input：输入框； select：选择框，目前只支持这两种格式，可以更具自己的需求进行拓展
      list: <Array>{      // 如果输入方式是选择框，需要一个选择列表值
        label: string | number
        value: string | number
      },
      listKeys: Null | { // 和list一直使用，指明label和value，一般label对应name，value对应id
        label: string,
        value: string
      }
      // ### 其它属性
      [string]: any // 继承el-table-column的所有属性
    }

  // 表格数据列表
    tableData: <number | string>[]

  // 需要监听的 method 方法
      pageSizeChange        切换页面显示条的大小触发
      currentPageChange     切换当前页触发
      selectChange          多选
      radioChange           单选

-->
<template>
  <div class="table--main">
    <el-table
      v-loading="loading"
      class="my-el-table"
      :data="tableData"
      border
      element-loading-background="rgba(55,55,55,0.4)"
      element-loading-text="数据正在加载中"
      element-loading-spinner="el-icon-loading"
      style="width: 100%; text-align: center;"
      @selection-change="handleSelectionChange"
    >
      <el-table-column
        v-if="showRadio"
        label="选项"
        width="55"
      >
        <template v-slot="{$index, row}">
          <input type="radio" name="my--radio" :checked="row[radioKey] === radioId" @change="handleRadioChange($index, row)">
        </template>
      </el-table-column>
      <el-table-column
        v-if="showSelect"
        type="selection"
        width="55"
      />
      <el-table-column
        v-if="showIndex"
        label="序号"
        type="index"
        width="64px"
      />
      <el-table-column
        v-for="(col,index) in tableColumn"
        :key="index"
        v-bind="col"
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
            {{ scope.row[col.prop] }}
          </template>
        </template>
      </el-table-column>
    </el-table>

    <div v-if="showPage" class="page-box">
      <el-pagination
        :current-page="pageOptions.page_num"
        :page-size="pageOptions.page_size"
        :total="pageOptions.total"
        @current-change="handleCurrentChange"
      />
    </div>
  </div>
</template>
<script>
// import pageOptions from '@/utils/pageOptions'
import HandleInput from './handle-input'
export default {
  name: 'BaseTable',
  components: { HandleInput },
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
          page_size: 20,
          page_num: 1,
          total: 0
        }
      }
    }
  },
  data() {
    return {
      radioId: ''
    }
  },
  methods: {
    // 多选
    handleSelectionChange(value) {
      this.$emit('selectChange', value)
    },

    // 单选
    handleRadioChange($index, row) {
      // radioKey可能是ID userid articleid这样的关键词，通过ID的比对来判断radio是否勾选
      this.radioId = row[this.radioKey]
      this.$emit('radioChange', {$index, row})
    },

    // 切换当前页
    handleCurrentChange(val) {
      this.$emit('currentPageChange', val)
    }
  }
}
</script>

