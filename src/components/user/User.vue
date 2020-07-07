<template>
  <div>
    <!-- 面包屑导航区 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>用户管理</el-breadcrumb-item>
      <el-breadcrumb-item>用户列表</el-breadcrumb-item>
    </el-breadcrumb>

    <!-- 卡片视图 -->
    <el-card>
      <!-- 搜索跟添加用户按钮 -->
      <el-row :gutter="20">
        <el-col :span="6">
          <el-input placeholder="请输入内容" v-model="queryInfo.query" clearable @clear="getUserList">
            <el-button slot="append" icon="el-icon-search" @click="getUserList"></el-button>
          </el-input>
        </el-col>

        <el-col :span="4">
          <el-button type="primary">添加用户</el-button>
        </el-col>
      </el-row>

			<!-- 用户列表区域 -->
			<el-table :data="userList" border stripe>
        <el-table-column type="index" label="#"></el-table-column>
        <el-table-column prop="username" label="姓名"></el-table-column>
        <el-table-column prop="email" label="邮箱"></el-table-column>
        <el-table-column prop="mobile" label="电话"></el-table-column>
        <el-table-column prop="role_name" label="角色"></el-table-column>

				<!-- 状态栏 -->
        <el-table-column label="状态">
          <template v-slot="slotProps">
            <el-switch v-model="slotProps.row.mg_state" @change="userStateChanged(slotProps.row)"></el-switch>
          </template>
        </el-table-column>

				<!-- 操作栏 -->
        <el-table-column label="操作">
          <template slot-scope="scope">
            <el-button
              type="primary"
              icon="el-icon-edit"
              size="mini"
              circle
              @click="showEditDialog(scope.row.id)"
            ></el-button>

            <el-button
              type="danger"
              icon="el-icon-delete"
              size="mini"
              circle
              @click="removeUserById(scope.row.id)"
            ></el-button>
            
            <el-tooltip
              class="item"
              effect="dark"
              content="角色分配"
              :enterable="false"
              placement="top"
            >
              <el-button
                type="warning"
                icon="el-icon-setting"
                size="mini"
                circle
                @click="showSetRole(scope.row)"
              ></el-button>
            </el-tooltip>
          </template>
        </el-table-column>
			</el-table>

      <!-- 分页区域 -->
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="queryInfo.pagenum"
        :page-sizes="[5, 10, 15]"
        :page-size="queryInfo.pagesize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="total"
      ></el-pagination>
    </el-card>
  </div>
</template>

<script>
export default {
	data () {
		return {
			queryInfo: {
				query: '',
				pagenum: 1,
				pagesize: 5
			},
			userList: [],
			total: 0
		}
	},
	created () {
		this.getUserList()
	},
	methods: {
		// 获取用户列表数据
		async getUserList () {
			const { data: res } = await this.$http.get('users', { 
				params: this.queryInfo
			 })
			 if (res.meta.status !== 200) {
				 return this.$message.error('获取用户列表失败！')
			 }
			 this.userList = res.data.users
			 this.total = res.data.total
			 console.log(res)
		},

    // 监听 pagesize 改变的事件
    handleSizeChange (newSize) {
      this.queryInfo.pagesize = newSize
      this.getUserList()
		},
		
    // 监听 页码值 改变事件
    handleCurrentChange (newSize) {
      // console.log(newSize)
      this.queryInfo.pagenum = newSize
      this.getUserList()
		},
		
    // 监听 switch开关 状态改变
    async userStateChanged (userInfo) {
      const { data: res } = await this.$http.put(
        `users/${userInfo.id}/state/${userInfo.mg_state}`
      )
      if (res.meta.status !== 200) {
        userInfo.mg_state = !userInfo.mg_state
        return this.$message.error('更新用户状态失败')
      }
      this.$message.success('更新用户状态成功！')
    }
	}
}
</script>

<style lang="less" scoped>
</style>
