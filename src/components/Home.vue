<template>
  <el-container class="home-container">
    <!-- 头部 -->
    <el-header>
      <div>
        <img src="../assets/logo.png" alt />
        <span>电商后台管理系统</span>
      </div>
      <el-button type="info" @click="logout">退出</el-button>
    </el-header>

    <!-- 主体 -->
    <el-container>

      <!-- 侧边栏 -->
      <el-aside :width="isCollapse ? '64px' : '180px'">

        <!-- 侧边栏菜单 -->
        <el-menu
            background-color="#323744"
            text-color="#fff"
            active-text-color="#2a6ab4"
            :collapse="isCollapse"
            :collapse-transition="false"
            router
            :default-active="$route.path">

          <!-- 菜单折叠按钮 -->
          <div class="toggle-button" @click="togleCollapse">|||</div>

          <!-- 一级菜单 -->
          <el-submenu :index="item.id + ''" v-for="item in menuList" :key="item.id">

            <!-- 一级菜单模板（图标、名称 -->
            <template slot="title">
              <i :class="iconObj[item.id]"></i>
              <span>{{ item.authName }}</span>
            </template>

            <!-- 二级菜单 -->
            <el-menu-item :index="'/' + subItem.path" v-for="subItem in item.children" :key="subItem.id">

              <!-- 二级菜单模板（图标、名称 -->
              <template slot="title">
                <i class="el-icon-menu"></i>
                <span>{{ subItem.authName }}</span>
              </template>
            </el-menu-item>

          </el-submenu>
        </el-menu>
      </el-aside>

      <el-main>
        <router-view></router-view>
      </el-main>
    </el-container>

  </el-container>
</template>

<script>
export default {
  data () {
    return {
      menuList: [],
      iconObj: {
        125: 'iconfont icon-user',
        103: 'iconfont icon-tijikongjian',
        101: 'iconfont icon-shangpin',
        102: 'iconfont icon-danju',
        145: 'iconfont icon-baobiao'
      },
      // 默认不折叠
      isCollapse: false
    }
  },
  created () {
    this.getMenuList()
  },
  methods: {
    // 登出
    logout () {
      window.sessionStorage.clear()
      this.$router.push('/login')
    },

    // 获取菜单
    async getMenuList () {
      const { data: res } = await this.$http.get('menus')
      console.log(res)
      if (res.meta.status !== 200) return this.$message.error(res.meta.msg)
      this.menuList = res.data
    },

    // 菜单的折叠与展开
    togleCollapse () {
      this.isCollapse = !this.isCollapse
    }
  }
}
</script>

<style lang="less" scoped>
.home-container {
  height: 100%;
}

.el-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding-left: 0;
  background-color: #373f41;
  color: #fff;
  font-size: 20px;
  div {
    display: flex;
    align-items: center;
    img {
      height: 40px;
    }
    span {
      margin-left: 15px;
    }
  }
}

.el-aside {
  background-color: #333744;

  .el-menu {
    border: none;
  }
}

.el-main {
  background-color: #eaedf1;
}

.iconfont {
  margin-right: 10px;
}

.toggle-button {
  background-color: #4a5064;
  font-size: 10px;
  line-height: 24px;
  color: #fff;
  text-align: center;
  letter-spacing: 0.2em;
  cursor: pointer;
}
</style>
