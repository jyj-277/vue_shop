<template>
  <el-container class="out">
    <!--头部区域-->
    <el-header>
      <div>
        <img src="../assets/logo.png" />
        <span>后台管理系统</span>
      </div>
      <el-button type="info" @click="logout">退出</el-button>
    </el-header>
    <!--页面主体-->
    <el-container>
      <!--侧边栏-->
      <el-aside :width="isCollapse ? '64px' : '200px'">
        <div class="toogle-button" @click="toogle">|||</div>
        <!--侧边栏菜单-->
        <el-menu
          background-color="#2b4b6b"
          text-color="#fff"
          active-text-color="#4094ff"
          :collapse="isCollapse"
          :collapse-transition="false"
          :router="true"
          :default-active="activePath"
        >
          <!--一级菜单-->
          <el-submenu
            :index="item.id + ''"
            v-for="item in menulist"
            :key="item.id"
          >
            <!--一级菜单的模板区域-->
            <template slot="title">
              <!--图标-->
              <i class="el-icon-location"></i>
              <!--文本-->
              <span>{{ item.authName }}</span>
            </template>
            <!--二级菜单-->
            <el-menu-item
              :index="'/' + subItem.path"
              v-for="subItem in item.children"
              :key="subItem.id"
              @click="saveNavState('/' + subItem.path)"
            >
              <template slot="title">
                <!--图标-->
                <i class="el-icon-menu"></i>
                <!--文本-->
                <span>{{ subItem.authName }}</span>
              </template>
            </el-menu-item>
          </el-submenu>
        </el-menu>
      </el-aside>
      <!--右侧内容主体-->
      <el-main>
        <router-view></router-view>
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
export default {
  created() {
    this.getMenuList()
    this.activePath = window.sessionStorage.getItem('activePath')
  },
  name: 'Home',
  data: function() {
    return {
      menulist: [],
      // 是否折叠
      isCollapse: false,
      // 被激活的链接地址
      activePath: ''
    }
  },
  methods: {
    logout() {
      window.sessionStorage.clear()
      this.$router.push('/login')
    },
    toogle() {
      this.isCollapse = !this.isCollapse
    },
    // 保存链接的激活状态
    saveNavState(activePath) {
      window.sessionStorage.setItem('activePath', activePath)
      this.activePath = activePath
    },
    // 获取所有的菜单
    async getMenuList() {
      const { data: res } = await this.$http.get('menus')
      if (res.meta.status !== 200) return this.$message.error(res.meta.msg)
      this.menulist = res.data
      // console.log(res)
    }
  }
}
</script>

<style lang="less" scoped>
.out {
  height: 100%;
  .el-header {
    width: 100%;
    background: #2b4b6b;
    display: flex;
    justify-content: space-between;
    padding-left: 0;
    align-items: center;
    > div {
      /* vertical-align: middle; */
      display: flex;
      align-items: center;
      img {
        width: 50px;
        height: 50px;
        border-radius: 10px;
      }
      span {
        color: aliceblue;
        font-size: 20px;
        margin-left: 15px;
      }
    }
  }

  .el-aside {
    background: #2b4b6b;
    .toogle-button {
      background: grey;
      text-align: center;
      color: white;
      line-height: 24px;
      font-size: 10px;
      letter-spacing: 0.2em;
      cursor: pointer;
    }
    .el-menu {
      border-right: none;
    }
  }
}
</style>
