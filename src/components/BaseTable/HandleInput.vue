<!--
  目前就两种输入格式，你可以根据自己的需求进行更改
-->

<template>
  <div>
    <template v-if="editType === 'input'">
      <el-input
        :value="value"
        v-bind="$attrs"
        @input="handleChange"></el-input>
    </template>
    <template v-else-if="editType === 'select'">
      <el-select
        :value="value"
        v-bind="$attrs"
        @change="handleChange">
        <el-option
          v-for="(item, index) in lists"
          :key="index"
          v-bind="{...item}"
        ></el-option>
      </el-select>
    </template>
  </div>
</template>

<script>
export default {
  name: 'HandleInput',
  inheritAttrs: false,
  props: {
    value: [String, Number],
    editType: {
      type: String,
      default: 'input'
    },
    list: {
      type: Array,
      default() {
        return [{
          label: 'zhangsan',
          value: '1'
        }, {
          label: '李四',
          value: '2'
        }]
      }
    },
    listKeys: {
      type: Object,
      default: null // { label: 'name', value: 'id' }
    }
  },
  data() {
    return {

    }
  },
  computed: {
    lists() {
      const listKeys = Object.prototype.toString.call(this.listKeys) === '[object Object]' 
        ? Object.assign({}, this.listKeys)
        : null
      const list = [...this.list]
      if (!listKeys) {
        return list
      }
      return this.list.map(item => ({
        label: item[listKeys['label']],
        value: item[listKeys['value']]
      }))
    }
  },
  methods: {
    handleChange(value) {
      console.log(value)
      this.$emit('input', value)
    }
  }
}
</script>

<style lang="scss" scoped>

</style>