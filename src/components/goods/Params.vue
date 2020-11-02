<template>
  <div>
    <!--面包屑导航区域-->
    <el-breadcrumb>
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>商品管理</el-breadcrumb-item>
      <el-breadcrumb-item>商品列表</el-breadcrumb-item>
    </el-breadcrumb>

    <!--卡片区域-->
    <el-card>
      <el-row>
        <el-col>
          <el-alert
            title=" 注意:只允许为第三级分类设置相关参数!"
            type="warning"
            show-icon
            :closable="false"
          >
          </el-alert>
        </el-col>
      </el-row>
      <el-row class="cas">
        <el-col>
          <span style="font-size:14px ;margin:0 10px ">选择商品分类:</span
          ><el-cascader
            expand-trigger="hover"
            :options="cateList"
            :props="cateProps"
            v-model="selectedCateKeys"
            @change="handleChange"
            porps.checkStrictly
            clearable
          >
          </el-cascader>
        </el-col>
      </el-row>
      <el-row>
        <el-col>
          <el-tabs v-model="activeName" @tab-click="handleTabClick">
            <!--添加动态参数面板-->
            <el-tab-pane label="动态参数" name="many">
              <el-button
                type="primary"
                :disabled="isDisabled"
                @click="addDialog"
                >添加参数</el-button
              >
              <!--动态参数表格-->
              <el-table :data="manyTabData" style="width: 100%" border stripe>
                <!--展开列-->
                <el-table-column type="expand">
                  <template slot-scope="scope">
                    <el-tag
                      v-for="(item, i) in scope.row.attr_vals"
                      :key="i"
                      closable
                      @close="handleClose(i, scope.row)"
                    >
                      {{ item }}
                    </el-tag>
                    <el-input
                      class="input-new-tag"
                      v-if="scope.row.inputVisible"
                      v-model="scope.row.inputValue"
                      ref="saveTagInput"
                      size="small"
                      @keyup.enter.native="handleInputConfirm(scope.row)"
                      @blur="handleInputConfirm(scope.row)"
                    >
                    </el-input>
                    <el-button
                      v-else
                      class="button-new-tag"
                      size="small"
                      @click="showInput(scope.row)"
                      >+ New Tag</el-button
                    >
                  </template>
                </el-table-column>
                <!--索引列-->
                <el-table-column type="index" label="序号" width="50">
                </el-table-column>
                <el-table-column label="参数名称" prop="attr_name">
                </el-table-column>
                <el-table-column label="操作">
                  <template slot-scope="scope">
                    <el-button
                      size="mini"
                      type="primary"
                      icon="el-icon-edit"
                      @click="showEditDialog(scope.row.attr_id)"
                      >编辑</el-button
                    >
                    <el-button
                      size="mini"
                      type="danger"
                      icon="el-icon-delete"
                      @click="showDeleteDialog(scope.row.attr_id)"
                      >删除</el-button
                    >
                  </template>
                </el-table-column>
              </el-table>
            </el-tab-pane>
            <!--添加静态属性面板-->
            <el-tab-pane label="静态属性" name="only">
              <el-button
                type="primary"
                :disabled="isDisabled"
                @click="addDialog"
                >添加属性</el-button
              >
              <!--静态属性表格-->
              <el-table :data="onlyTabData" style="width: 100%" border stripe>
                <!--展开列-->
                <el-table-column type="expand">
                  <template slot-scope="scope">
                    <el-tag
                      v-for="(item, i) in scope.row.attr_vals"
                      :key="i"
                      closable
                      @close="handleClose(i, scope.row)"
                    >
                      {{ item }}
                    </el-tag>
                    <el-input
                      class="input-new-tag"
                      v-if="scope.row.inputVisible"
                      v-model="scope.row.inputValue"
                      ref="saveTagInput"
                      size="small"
                      @keyup.enter.native="handleInputConfirm(scope.row)"
                      @blur="handleInputConfirm(scope.row)"
                    >
                    </el-input>
                    <el-button
                      v-else
                      class="button-new-tag"
                      size="small"
                      @click="showInput(scope.row)"
                      >+ New Tag</el-button
                    >
                  </template>
                </el-table-column>
                <!--索引列-->
                <el-table-column type="index" label="序号" width="50">
                </el-table-column>
                <el-table-column label="属性名称" prop="attr_name">
                </el-table-column>
                <el-table-column label="操作">
                  <template slot-scope="scope">
                    <el-button
                      size="mini"
                      type="primary"
                      icon="el-icon-edit"
                      @click="showEditDialog(scope.row.attr_id)"
                      >编辑</el-button
                    >
                    <el-button
                      size="mini"
                      type="danger"
                      icon="el-icon-delete"
                      @click="showDeleteDialog(scope.row.attr_id)"
                      >删除</el-button
                    >
                  </template>
                </el-table-column>
              </el-table>
            </el-tab-pane>
          </el-tabs>
        </el-col>
      </el-row>
    </el-card>

    <!--添加属性对话框-->

    <el-dialog
      :title="'添加' + titleTxt"
      :visible.sync="addDialogVisible"
      width="50%"
      @close="addDialogClosed"
    >
      <el-form
        :model="addForm"
        :rules="addFormRules"
        ref="addFormRef"
        label-width="100px"
      >
        <el-form-item :label="titleTxt" prop="attr_name">
          <el-input v-model="addForm.attr_name"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="addDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="addParams">确 定</el-button>
      </span>
    </el-dialog>

    <!--修改属性对话框-->
    <el-dialog
      :title="'修改' + titleTxt"
      :visible.sync="editDialogVisible"
      width="50%"
      @close="editDialogClosed"
    >
      <el-form
        :model="editForm"
        :rules="editFormRules"
        ref="editFormRef"
        label-width="100px"
      >
        <el-form-item :label="titleTxt" prop="attr_name">
          <el-input v-model="editForm.attr_name"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="editDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="editParams">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: 'Params',
  data: function() {
    return {
      // 商品分类列表
      cateList: [],
      // 级联选择框的配置对象
      cateProps: {
        value: 'cat_id',
        label: 'cat_name',
        children: 'children'
      },
      // 级联选择框双向绑定到的数组
      selectedCateKeys: [],
      // 被激活的页签名称
      activeName: 'many',
      // 动态参数数据列表
      manyTabData: [],
      // 静态属性数据列表
      onlyTabData: [],
      // 控制添加属性对话框的显示与隐藏
      addDialogVisible: false,
      // 添加属性的表单数据对象
      addForm: {
        attr_name: ''
      },
      // 添加属性的表单验证规则
      addFormRules: {
        attr_name: [
          { required: true, message: '请输入参数名称', trriger: 'blur' }
        ]
      },
      // 控制修改属性对话框的显示与隐藏
      editDialogVisible: false,
      // 修改属性的表单数据对象
      editForm: {
        attr_name: ''
      },
      // 修改属性的表单的验证规则
      editFormRules: {
        attr_name: [
          { required: true, message: '请输入参数名称', trriger: 'blur' }
        ]
      }
    }
  },
  created() {
    this.getCateList()
  },
  mounted() {},
  methods: {
    async getCateList() {
      const { data: res } = await this.$http.get('categories/')
      if (res.meta.status !== 200) {
        return this.$message.error('获取参数列表失败!')
      }
      this.cateList = res.data
    },
    // 级联选择框选项发生变化会触发这个函数
    handleChange() {
      this.getParams()
    },
    async getParams() {
      // 数组长度不为3,证明选择的不是3级分类
      if (this.selectedCateKeys.length !== 3) {
        this.selectedCateKeys = []
        this.manyTabData = []
        this.onlyTabData = []
        return
      }
      // 是三级分类
      console.log(this.selectedCateKeys)
      const { data: res } = await this.$http.get(
        `categories/+${this.cateId}+/attributes`,
        {
          params: {
            sel: this.activeName
          }
        }
      )
      if (res.meta.status !== 200) {
        return this.$message.error('获取参数列表失败!')
      }

      res.data.forEach(item => {
        item.attr_vals = item.attr_vals ? item.attr_vals.split(' ') : []
        item.inputVisible = false
        item.inputValue = ''
      })
      console.log(res.data)
      if (this.activeName === 'many') {
        this.manyTabData = res.data
      } else {
        this.onlyTabData = res.data
      }
    },
    handleTabClick() {
      // console.log(this.activeName)
      this.getParams()
    },
    addDialog() {
      this.addDialogVisible = true
    },
    addDialogClosed() {
      this.$refs.addFormRef.resetFields()
    },
    // 点击确定，添加属性/参数
    addParams() {
      this.$refs.addFormRef.validate(async valid => {
        if (!valid) return
        const { data: res } = await this.$http.post(
          `categories/+${this.cateId}+/attributes`,
          {
            attr_name: this.addForm.attr_name,
            attr_sel: this.activeName
          }
        )
        if (res.meta.status !== 201) {
          return this.$message.error('添加参数失败！')
        }
        this.$message.success('添加参数成功！')
        this.addDialogVisible = false
        this.getParams()
      })
    },
    // 点击编辑，修改属性/参数
    async showEditDialog(attrId) {
      // 查询当前参数的信息
      const { data: res } = await this.$http.get(
        `categories/${this.cateId}/attributes/${attrId}`,
        {
          params: {
            attr_sel: this.activeName
          }
        }
      )
      if (res.meta.status !== 200) {
        return this.$message.error('获取参数信息失败！')
      }
      this.editForm = res.data
      this.editDialogVisible = true
    },
    // 关闭编辑对话框，重置表单
    editDialogClosed() {
      this.$refs.editFormRef.resetFields()
    },
    // 点击确定，修改参数/属性
    editParams() {
      this.$refs.editFormRef.validate(async valid => {
        if (!valid) return
        const { data: res } = await this.$http.put(
          `categories/${this.cateId}/attributes/${this.editForm.attr_id}`,
          {
            attr_name: this.editForm.attr_name,
            attr_sel: this.activeName
          }
        )
        if (res.meta.status !== 200) {
          return this.$message.error('修改参数失败！')
        }
        this.$message.success('修改参数成功！')
        this.getParams()
        this.editDialogVisible = false
      })
    },
    // 点击删除，弹出对话框
    async showDeleteDialog(attrId) {
      const confirmResult = await this.$confirm(
        '此操作将永久删除该参数, 是否继续?',
        '提示',
        {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }
      ).catch(err => err)
      // 用户取消删除操作
      if (confirmResult !== 'confirm') {
        return this.$message.error('已取消删除')
      }
      // 删除业务逻辑
      const { data: res } = await this.$http.delete(
        `categories/${this.cateId}/attributes/${attrId}`
      )
      if (res.meta.status !== 200) {
        return this.$message.error('删除参数失败！')
      }
      this.$message.success('删除参数成功！')
      this.getParams()
    },
    // 文本框处理函数
    async handleInputConfirm(row) {
      if (row.inputValue.trim().length === 0) {
        row.inputValue = ''
        row.inputVisible = false
        return
      }
      // 如果没有return 则输入的内容需要做后续处理
      row.attr_vals.push(row.inputValue.trim())
      row.inputValue = ''
      row.inputVisible = false
      this.saveAttrVals(row)
    },
    //
    async saveAttrVals(row) {
      // 需要发起请求保存这次操作
      const { data: res } = await this.$http.put(
        `categories/${this.cateId}/attributes/${row.attr_id}`,
        {
          attr_name: row.attr_name,
          attr_sel: row.attr_sel,
          attr_vals: row.attr_vals.join(' ')
        }
      )
      if (res.meta.status !== 200) {
        this.$message.error('修改参数失败!')
      }
      this.$message.success('修改参数成功!')
    },
    showInput(row) {
      row.inputVisible = true
      // 只有当页面元素重新渲染之后，才会执行回调函数
      this.$nextTick(_ => {
        this.$refs.saveTagInput.$refs.input.focus()
      })
    },
    // 关闭参数tag标签
    handleClose(i, row) {
      row.attr_vals.splice(i, 1)
      this.saveAttrVals(row)
    }
  },
  computed: {
    isDisabled() {
      if (this.selectedCateKeys.length === 3) {
        return false
      }
      return true
    },
    cateId() {
      if (this.selectedCateKeys.length === 3) {
        return this.selectedCateKeys[2]
      }
      return null
    },
    titleTxt() {
      if (this.activeName === 'many') {
        return '动态参数'
      } else {
        return '静态属性'
      }
    }
  }
}
</script>

<style lang="less" scoped>
.el-card {
  margin-top: 15px;
  box-shadow: 0 1px 1px rgba(0, 0, 0, 0.15) !important;
}
.cas {
  margin: 10px 0;
}
.el-table {
  margin-top: 15px !important;
}
.el-tag {
  margin: 5px;
}
.input-new-tag {
  width: 120px;
}
</style>
