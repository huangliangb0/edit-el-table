<template>
  <el-pagination
    class="el-pagination--box"
    layout="total, prev, pager, next"
    prev-text="上一页"
    next-text="下一页"
    :total="total"
    :page-size="pageSize"
    :current-page='currentPage'
    @current-change="handleCurrentChange"
  />
</template>

<script>
export default {
  name: 'Myel-pagination',
  props: {
    total: {
      type: Number,
      default: 0
    },
    pageSize: {
      type: Number,
      default: 10
    },
    currentPage: {
      type: Number,
      default: 1
    }
  },
  methods: {
    handleCurrentChange(current) {
      this.$emit('current-change', current)
    }
  }
}
</script>

<style lang="scss" scoped>
.el-pagination--box {
  padding-top: 30px;
  padding-bottom: 30px;
  ::v-deep button {
    border: 1px solid #C0C4CC;
    border-radius: 2px;
    &:hover {
      border-color: blue;
    }
    &[disabled] {
      border-color: #C0C4CC;
    }
  }
  ::v-deep .el-pager {
    margin: 0 10px;
  }
  ::v-deep .number {
    position: relative;
    margin: 0 5px;
    &::after {
      display: inline-block;
      content: '';
      position: absolute;
      left: 0;
      right: 0;
      top: 0;
      bottom: 0;
      border: 1px solid #C0C4CC;
      border-radius: 2px;
    }
    &:hover, &.active {
      color: blue;
      &::after {
        border-color: blue;
      }
    }
  }
}
</style>
