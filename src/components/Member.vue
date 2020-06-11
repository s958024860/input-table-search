<template>
  <div class="member">
    <el-table
      :data="members"
      highlight-current-row
      height="295"
      style="width: 100%;"
      :row-class-name="tableRowClassName"
      @row-click="handleRowClick"
    >
      <el-table-column
          prop="name"
          label="姓名"
          min-width="80"
          show-overflow-tooltip
      >
      </el-table-column>
      <el-table-column
          prop="sex"
          label="性别"
          min-width="80"
          show-overflow-tooltip
      >
      </el-table-column>
      <el-table-column
          prop="phoneNo"
          label="手机号"
          min-width="120"
          show-overflow-tooltip
      >
      </el-table-column>
      <el-table-column
          prop="address"
          label="住址"
          min-width="80"
          show-overflow-tooltip
      >
      </el-table-column>
      <el-table-column
          prop="firstOrderTime"
          label="首次订单时间"
          min-width="150"
          show-overflow-tooltip
      >
      </el-table-column>
      <el-table-column
          prop="recentOrderTime"
          label="最近订单时间"
          min-width="150"
          show-overflow-tooltip
      >
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
  export default {
    name: 'Member',
    props: {
      members: {
        type: Array,
        default: () => []
      },
      // 当前选中行
      currentRowIndex: {
        type: Number,
        default: -1
      },
    },
    methods: {
      // 选中当前行样式
      tableRowClassName ({ rowIndex }) {
        return rowIndex === this.currentRowIndex
          ? 'active-row'
          : ''
      },
      handleRowClick (row) {
        if (row) this.$emit('setMemberInfo', row)
      },
    }
  }
</script>

<style>
  .member .active-row {
    background: #e7f5f3;
  }
  .member .el-table__row {
    cursor: pointer;
  }
</style>
