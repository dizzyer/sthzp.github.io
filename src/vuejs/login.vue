<template>
    <div class="login-container">
        
            <div class="login-box">
                <div class="login-text">订单管理</div>
            <el-form status-icon ref="loginformRef" class="login-form" :model="loginform" :rules="loginrules"> 
                <el-form-item prop="username">
                    <el-input  v-model="loginform.username" prefix-icon="el-icon-user-solid"></el-input>
                </el-form-item>
                <el-form-item prop="password">
                    <el-input  v-model="loginform.password" prefix-icon="el-icon-s-goods" type="password"></el-input>
                </el-form-item>
                <el-form-item class="btns">
                    <el-button type="primary"  @click="submitForm">登录</el-button>
                    <el-button type="info"  @click="resetForm">重置</el-button>
                </el-form-item>
            </el-form>
        </div>
        
    </div>
</template>

<script>
export default {
    data(){
        return{
            loginform:{
                username:'',
                password:'',
            },
            loginrules:{
                username:[
                    {required: true, message:"请输入用户名", trigger:"blur"},
                    { min: 3, max: 10, message: '长度在 3 到 10 个字符', trigger: 'blur' },
                    ],
                password:[
                    { required: true, message: '请输入登录密码', trigger: 'blur' },
                    { min: 6, max: 15, message: '长度在 6 到 15 个字符', trigger: 'blur' },
                ],

            },
            }
        },
    methods:{
            submitForm() {
                this.$refs.loginformRef.validate(async valid => {
                    if (valid) {
                        const {data: res} =await this.$http.post('logintest', this.loginform);
                        console.log(res);
                        if(res.success !== "yes"){
                            return this.$message.error('登陆失败！')
                        }
                        this.$message.success("登陆成功！！！")
                        window.sessionStorage.setItem('token', res.token)
                        this.$router.push('/home')
                    } else {
                        console.log('error submit!!');
                        return false;
                    }
                });
            },
            resetForm() {
                this.$refs.loginformRef.resetFields();
            }
    }
}
</script>

<style lang="less" scoped>
.login-container {
    
    height: 100%;
    width: 100%;
    background-image: linear-gradient( 135deg, #52E5E7 10%, #130CB7 100%);
    }
.login-box {
    width:300px;
    height: 360px;
    position: absolute;
    left: 50%;
    top:  50%;
    transform:translate(-50%,-50%);
    padding: 40px;
    border:0;
    border-radius: 5px;
    background-color:rgba(255, 255, 255, 0.856);
    box-shadow: 5px 5px 30px #1b1b1ba6;
}
.btns{
    display:flex;
    justify-content: center;
    margin-top:10%;
}
.login-form{
    // position: absolute;
    // bottom: 30px;
    // width: 95%;
    // padding:10px;
    // box-sizing: border-box;
    margin-top:10%;
}

.login-text{
    width: 100%;
    text-align: center;
    line-height: 100px;
    font-size: 40px;
    
    color:#000000a9;
    font-weight: 550;
    
}

</style>