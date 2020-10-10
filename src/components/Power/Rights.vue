<template>
  <div>
    <!--面包屑导航区域-->
    <el-breadcrumb>
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>权限管理</el-breadcrumb-item>
      <el-breadcrumb-item>权限列表</el-breadcrumb-item>
    </el-breadcrumb>

    <!--卡片区域-->
    <el-card>
      <el-table :data="rightsList" border stripe>
        <el-table-column
          type="index"
          label="序号"
          width="80px"
        ></el-table-column>
        <el-table-column label="权限名称" prop="authName"></el-table-column>
        <el-table-column label="路径" prop="path"></el-table-column>
        <el-table-column label="权限等级" prop="level">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.level === '0'">一级</el-tag>
            <el-tag type="success" v-else-if="scope.row.level === '1'"
              >二级</el-tag
            >
            <el-tag type="warning" v-else>三级</el-tag>
          </template>
        </el-table-column>
      </el-table>

      <!-- 分页区域-->
      <!-- <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="queryInfo.pagenum"
        :page-sizes="[5, 10, 20, 50]"
        :page-size="queryInfo.pagesize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="total"
      >
      </el-pagination> -->
    </el-card>
  </div>
</template>

<script>
export default {
  name: 'Rights',
  data: function() {
    return {
      // // 所有列表
      // // 获取权限列表的参数对象
      // queryInfo: {
      //   query: '',
      //   // 当前页数
      //   pagenum: 1,
      //   // 当前每页显示多少条数据
      //   pagesize: 2
      // },
      // total: 0,
      rightsList: []
    }
  },
  created() {
    // 获取所有权限
    this.getRightsList()
  },
  mounted() {},
  methods: {
    // 获取权限列表
    async getRightsList() {
      const { data: res } = await this.$http.get('rights/list')
      if (res.meta.status !== 200) {
        return this.$message.error('获取权限列表失败')
      }
      this.rightsList = res.data
    }

    // // 监听pagesize改变事件
    // handleSizeChange(newSize) {
    //   this.queryInfo.pagesize = newSize
    //   this.getRightsList()
    // },

    // // 监听 页码值 改变事件
    // handleCurrentChange(newPage) {
    //   // console.log(newPage)
    //   this.queryInfo.pagenum = newPage
    //   this.getRightsList()
    // }
  }
}
</script>

<style lang="less" scoped></style>
