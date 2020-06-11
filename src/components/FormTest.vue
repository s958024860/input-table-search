<template>
  <div class="form-test">
    <el-form ref="form" class="form" :model="form" :rules="rules">
      <el-form-item>
        <el-table
            :data="form.orderList"
            stripe
            style="width: 100%"
        >
          <el-table-column
              prop="consigneeName"
              label="收货人姓名"
          >
            <template slot-scope="scope">
              <el-form-item label="" :prop="`orderList.${scope.$index}.consigneeName`" :rules="rules.orderList.consigneeName">
                <el-popover
                    v-model="scope.row.visible"
                    placement="bottom-start"
                    title=""
                    trigger="manual"
                    width="800"
                >
                  <el-input
                      :ref="`member-input_${scope.$index}`"
                      slot="reference"
                      v-model="scope.row.consigneeName"
                      placeholder="姓名"
                      @keydown.native.esc.stop.prevent="displayNoneMedicineList(scope.row)"
                      @keydown.native.tab="displayNoneMedicineList(scope.row)"
                      @keydown.native.down.stop.prevent="navigateRow('next')"
                      @keydown.native.up.stop.prevent="navigateRow('prev')"
                      @keyup.enter.native="memberSearch(scope.row)"
                      @paste.native="e => { debounceSearch(e, scope.row) }"
                      @keyup.native="e => { debounceSearch(e, scope.row) }"
                  ></el-input>
                  <Member
                      :members="members"
                      :current-row-index="currentRowIndex"
                      @setMemberInfo="(value) => { setMemberInfo(scope.row, value) }"
                  />
                </el-popover>
              </el-form-item>
            </template>
          </el-table-column>
          <el-table-column
              prop="consigneeSex"
              label="收货人性别"
          >
            <template slot-scope="scope">
              <span>{{ scope.row.consigneeSex || '--' }}</span>
            </template>
          </el-table-column>
          <el-table-column
              prop="consigneePhoneNo"
              label="收货人电话"
          >
            <template slot-scope="scope">
              <span>{{ scope.row.consigneePhoneNo || '--' }}</span>
            </template>
          </el-table-column>
          <el-table-column
              prop="consigneeAddr"
              label="收货人地址"
          >
            <template slot-scope="scope">
              <span>{{ scope.row.consigneeAddr || '--' }}</span>
            </template>
          </el-table-column>
          <el-table-column
              prop="consignorName"
              label="发货人姓名"
          >
            <template slot-scope="scope">
              <span>{{ scope.row.consignorName || '--' }}</span>
            </template>
          </el-table-column>
          <el-table-column
              prop="consignorPhoneNo"
              label="发货人电话"
          >
            <template slot-scope="scope">
              <span>{{ scope.row.consignorPhoneNo || '--' }}</span>
            </template>
          </el-table-column>
          <el-table-column
              prop="consignorAddr"
              label="发货人地址"
          >
            <template slot-scope="scope">
              <span>{{ scope.row.consignorAddr || '--' }}</span>
            </template>
          </el-table-column>
          <el-table-column
              prop="remark"
              label="备注"
          >
            <template slot-scope="scope">
              <el-input
                  v-model="scope.row.remark"
                  @keyup.enter.native="enterRemark(scope.$index)"
              ></el-input>
            </template>
          </el-table-column>
          <el-table-column
              prop="operation"
              label=""
              width="80"
          >
            <template slot="header">
              <i
                  class="el-icon-circle-plus-outline"
                  style="cursor: pointer;"
                  @click="addOrder()"
              ></i>
            </template>
            <template slot-scope="scope">
              <el-button type="danger" icon="el-icon-delete" circle @click="delOrder(scope.$index)"></el-button>
            </template>
          </el-table-column>
        </el-table>
      </el-form-item>
    </el-form>
    <div class="operation">
      <el-button type="primary" @click="sendBatch">批量发货</el-button>
    </div>
  </div>
</template>

<script>
import Member from './Member'
import members from '../assets/json/member.json'
import _ from 'lodash'
const ORDER = {
  // 原收货人姓名
  originConsigneeName: '',
  // 收货人唯一标识
  consigneeCode: '',
  // 收货人姓名
  consigneeName: '',
  // 收货人性别
  consigneeSex: '',
  // 收货人电话
  consigneePhoneNo: '',
  // 收货人地址
  consigneeAddr: '',
  // 发货人姓名
  consignorName: 'Nancy',
  // 发货人电话
  consignorPhoneNo: '12345678923',
  // 发货人地址
  consignorAddr: '安道尔',
  // 备注
  remark: '',
  visible: false
}

export default {
  name: 'FormTest',
  components: {
    Member
  },
  data () {
    return {
      members: [],
      currentRowIndex: -1,
      form: {
        // 快递订单信息
        orderList: [ _.cloneDeep(ORDER) ],
      },
      rules: {
        orderList: {
          consigneeName: [
            { required: true, message: '收货人不能为空', trigger: 'blur' },
          ],
          remark: []
        }
      },
    }
  },
  methods: {
    // 批量发送
    sendBatch () {
      this.$refs['form'].validate(valid => {
        if (!valid) {
          this.$message.error('请检查表单项')
          return
        }
        this.$message.success(`成功发送${this.form.orderList.length}条订单`)
      })
    },
    // 嘱托回车
    async enterRemark (index) {
      if (index === this.form.orderList.length - 1) {
        await this.addOrder()
      } else {
        await this.$nextTick()
        await this.focusConsigneeNameName(index + 1)
      }
    },
    // 添加订单
    addOrder () {
      this.form.orderList.push(_.cloneDeep(ORDER))
      this.focusConsigneeNameName(this.form.orderList.length - 1)
    },
    async focusConsigneeNameName (index) {
      await this.$nextTick()
      this.$refs[`member-input_${index}`].focus()
    },
    // 删除订单
    delOrder (index) {
      if (this.form.orderList.length === 1) {
        this.$message.info('至少保存一条订单')
        return
      }
      this.form.orderList.splice(index, 1)
    },
    async setMemberInfo (row, value) {
      this.displayNoneMedicineList(row)
      // 带入会员信息
      this.$set(row, 'consigneeName', value.name)
      this.$set(row, 'originConsigneeName', value.name)
      this.$set(row, 'consigneeCode', value.id)
      this.$set(row, 'consigneeSex', value.sex)
      this.$set(row, 'consigneePhoneNo', value.phoneNo)
      this.$set(row, 'consigneeAddr', value.address)
      await this.$nextTick()
      this.clearValidate()
    },
    // 隐藏会员弹框
    displayNoneMedicineList (row) {
      this.$set(row, 'consigneeName', row.consigneeCode ? row.originConsigneeName : '')
      setTimeout(() => {
        this.members = []
        this.currentRowIndex = -1
        this.$set(row, 'visible', false)
      }, 250)
    },
    // 键盘上下键操作
    navigateRow (type) {
      if (this.members?.length) {
        if (type === 'next') {
          this.currentRowIndex += 1
          if (this.currentRowIndex === this.members.length) this.currentRowIndex = 0
        } else {
          this.currentRowIndex -= 1
          if (this.currentRowIndex < 0) this.currentRowIndex = this.members.length - 1
        }
      }
    },
    debounceSearch (event, row) {
      // 禁用指定按钮搜索 esc enter left right up down
      if (![ 37, 38, 39, 40, 9, 13, 27 ].includes(event.keyCode)) {
        this.memberSearch(row)
      }
    },
    // 会员搜索
    async memberSearch (row) {
      // 选中当前行
      if (
        row.visible &&
        this.currentRowIndex < this.members.length &&
        this.currentRowIndex > -1
      ) {
        await this.setMemberInfo(row, this.members[this.currentRowIndex])
      } else {
        // 搜索
        if (row.consigneeName) {
          // 清空列表
          this.$set(row, 'visible', true)
          const _members = JSON.parse(JSON.stringify(members))
          this.members = _members.filter(el => el.name.includes(row.consigneeName))
          this.currentRowIndex = -1
        }
      }
    },
    // 清除校验信息
    clearValidate () {
      this.$refs['form'].clearValidate()
    },
  },
}
</script>

<style scoped>
.form-test {
  height: 100%;
  width: 60%;
  margin: 0 auto;
  display: flex;
  flex-direction: column;
}
  .form {
    display: flex;
    flex: auto;
  }
  .operation {
    flex: none;
    height: 40px;
  }
  .el-form-item {
    margin-bottom: 0;
    width: 100%;
  }
</style>
