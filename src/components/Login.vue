<template>
  <div class="login-container">
    <div class="login-box">

      <!-- 头像区 -->
      <div class="avatar-box">
        <img src="../assets/logo.png" alt="avatar" />
      </div>

      <!-- 登录表单 -->
      <div>
        <el-form
          ref="loginFormRef"
          :model="loginForm"
          :rules="loginFormRules"
          label-width="60px"
          class="login-form"
        >
          <el-form-item label="账号" prop="username">
            <el-input v-model="loginForm.username" prefix-icon="iconfont icon-user"></el-input>
          </el-form-item>

          <el-form-item label="密码" prop="password">
            <el-input
              v-model="loginForm.password"
              type="password"
              prefix-icon="iconfont icon-3702mima"
            ></el-input>
          </el-form-item>

          <el-form-item class="btns">
            <el-button type="primary" @click="login">登录</el-button>
            <el-button type="info" @click="resetLoginForm">重置</el-button>
          </el-form-item>
        </el-form>
      </div>

    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      loginForm: {
        username: 'admin',
        password: '123456'
      },

      // 表单验证
      loginFormRules: {
        username: [
          { required: true, message: '请输入用户名', trigger: 'blur' },
          { min: 2, max: 10, message: '长度在 2 到 10 个字符', trigger: 'blur' }
        ],
        password: [
          { required: true, message: '请输入用户密码', trigger: 'blur' },
          { min: 6, max: 18, message: '长度在 6 到 18 个字符', trigger: 'blur' }
        ]
      }
    }
  },

  methods: {
    // 表单重置按钮
    resetLoginForm () {
      // resetFields：element-ui提供的表单方法
      this.$refs.loginFormRef.resetFields()
    },

    login () {
      // 表单预验证
      this.$refs.loginFormRef.validate(async valid => {
        if (!valid) return false

        // this.$http.post('login', this.loginForm): 返回值为 promise
        // 返回值为 promise，可加 await 简化操作 相应的也要加 async
        // 通过解构对象获得数据 data，并重新命名为 res
        const { data: res } = await this.$http.post('login', this.loginForm)

        if (res.meta.status !== 200) return this.$message.error('登录失败')
        this.$message.success('登录成功')

        // token 只应在当前网站打开期间生效，所以保存在 sessionStorage 中而不是 localStorage
        window.sessionStorage.setItem('token', res.data.token)

        // 通过路由跳转到后台主页
        this.$router.push('/home')
      })
    }
  }
}
</script>

<style lang="less" scoped>
.login-container {
  height: 100%;
  background-color: #2b4b6b;
}

.login-box {
  position: absolute;
  left: 50%;
  top: 50%;

  width: 450px;
  height: 300px;
  border-radius: 3px;
  transform: translate(-50%, -50%);
  background-color: #fff;

  .avatar-box {
    position: absolute;
    left: 50%;
    padding: 10px;
    width: 130px;
    height: 130px;
    border: 1px solid #eee;
    border-radius: 50%;
    box-shadow: 0 0 10px #ddd;
    transform: translate(-50%, -50%);
    background-color: #fff;

    img {
      width: 100%;
      height: 100%;
      border-radius: 50%;
      background-color: #eee;
    }
  }
}

.login-form {
  position: absolute;
  bottom: 0px;
  padding: 0 20px;
  width: 100%;
  box-sizing: border-box;
}

.btns {
  display: flex;
  justify-content: flex-end;
}
</style>
