<template>
    <div class="login">
			<div class="login-top">
				<i class="icon iconfont icon-kanbaozhi"></i> 微微日报登录
			</div>
      <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="50px" class="demo-ruleForm">
        
        <div class="login-center">
          <el-form-item label="账号" prop="username">
            <el-input v-model="ruleForm.username" autocomplete="on"></el-input>
          </el-form-item>
        </div>
        
        <div class="login-center">
          <el-form-item label="密码" prop="password">
            <el-input type="password" v-model="ruleForm.password" autocomplete="on"></el-input>
          </el-form-item>
        </div>

        <div class="login-center clearfix">
          <span class="lf" @click="clearCookie" style="cursor: pointer;color: #f19149;font-size: 0.75rem;margin-left: 5px;">忘记密码？</span>
          <div class="rf">
            <el-checkbox v-model="checked" style="color:#a0a0a0;">一周内自动登录</el-checkbox>
          </div>
        </div>

        <div class="login-center">
          <el-form-item>
            <el-button type="primary" @click="submitForm('ruleForm')" style="width:100%;">登录</el-button>
          <!--  <el-button @click="resetForm('ruleForm')">重置</el-button> -->
          </el-form-item>
        </div>

      </el-form>
    </div>
  
  

</template>

<script>
export default {
   data() {
      return {
        ruleForm: {
          username: '',
          password:''
        },
        rules: {
          username: [
            { required: true, message: '请输入账号', trigger: 'blur' },
            { min: 1, max: 10, message: '长度在 0 到 10 个字符', trigger: 'blur' }
          ],
          password: [
            { required: true, message: '请输入密码', trigger: 'blur' },
            { min: 6, max: 16, message: '长度在 6 到 16 个字符', trigger: 'blur' }
          ]
        },
        checked: false
    
      };
    },
    //页面加载调用获取cookie值
    updated() {
        this.getCookie();
    },
    methods: {
      submitForm(formName) {
        this.$refs[formName].validate((valid) => {
          if (valid) {
              const self = this;
              //判断复选框是否被勾选 勾选则调用配置cookie方法
              if (self.checked == true) {
                  //传入账号名，密码，和保存天数3个参数
                  self.setCookie(self.ruleForm.username, self.ruleForm.password, 7);
              }else {
                console.log("清空Cookie");
                //清空Cookie
                self.clearCookie();
              }
            
              this.getAjaxLogin();
           
          } else {
            console.log('error submit!!');
            return false;
          }
        });
      },
      //登录
      getAjaxLogin:async function(){
        let param = new URLSearchParams()
            param.append('username', this.ruleForm.username)
            param.append('password', this.ruleForm.password)
        let user = await this.$http({
            method:'post',
            data:param,
            url:this.$apiList['login']
        }); 
        if(user.data==""){
            alert('账户或用户名错误!');
            return;
        }else{
            // 将用户放入localStorage
            localStorage.setItem("user",JSON.stringify(user.data));
            // 验证成功进入首页 
            this.$router.push({ name: 'hemoPage'});
        }
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="less" scoped> 
@import '../assets/style/mixin';

.login{
  z-index: 2;
  position:absolute;
  border-radius: 5px;
  .mx_wh(350px,500px);
  background: white;
  box-shadow: 0px 0px 5px #333333;
  top: 50%;
  left: 50%;
  margin-top: -250px;
  margin-left: -175px;
  transition: all 1s;
  -moz-transition: all 1s;	/* Firefox 4 */
  -webkit-transition: all 1s;	/* Safari 和 Chrome */
  -o-transition: all 1s;	/* Opera */
  .login-top{
    font-size: 24px;
    margin-top: 100px;
    padding-left: 40px;
    box-sizing: border-box;
    color: #333333;
    text-align: left;
    margin-bottom: 30px;
  }
  .login-center{
    width: 100%;
    box-sizing: border-box;
    padding: 0 40px;
    margin-bottom: 30px;
    .lf{
      float: left;
    }
    .rf{
      float:right;
    }
  }
  .clearfix:before,
  .clearfix:after {
    display: table;
    content: "";
  }
  .clearfix:after {
    clear: both
  }
  .iconfont{
      font-size:26px;
      color:rgb(54, 124, 221);
  }
}
  
</style>
