<template>
    
        
    <el-container class="home-container">
        <!-- 头部 -->
  <el-header height="60px"> 
      <div>
          <span>主页</span>
      </div>
      <el-button type="info" size="mini"  @click="logout">退出</el-button>
  </el-header>
        <!-- 主体 -->
  <el-container>
        <!-- 左侧边栏 -->
    <el-aside width="225px">
        <el-menu
        active-text-color="#1989fa"
        background-color="#212529"
        router
        :default-active="activeIndex"
        text-color="#909294"
      >
        <el-submenu index="1">
          <template #title>
            <i class="el-icon-user"></i>
            <span style="font-size:16px">用户管理</span>
          </template>
          <el-menu-item-group >
            <el-menu-item index="userlist" @click="saveIndex('userlist')">用户列表</el-menu-item>
          </el-menu-item-group>
        </el-submenu>
        <el-submenu index="2">
            <template #title>
             <i class="el-icon-s-order"></i>
             <span style="font-size:16px">订单管理</span>
            </template>
          <el-menu-item-group >
            <el-menu-item index="change" @click="saveIndex('change')">订单列表</el-menu-item>
            <!-- <el-menu-item index="statistics" @click="saveIndex('statistics')">信息统计</el-menu-item> -->
          </el-menu-item-group>
        </el-submenu>
      </el-menu>
    </el-aside>
        <!-- 右侧内容 -->
    <el-main>
        <router-view></router-view>
    </el-main>
  </el-container>
</el-container>
</template>

<script>

export default {
    data() {
        return{
            activeIndex: ''
        }
    },
    created(){
        this.activeIndex = window.localStorage.getItem('activeIndex')
    },
    methods: {
        logout(){
            window.sessionStorage.clear();
            this.$router.replace("/login")
        },
        saveIndex(index){
            window.localStorage.setItem('activeIndex',index)
            this.activeIndex = index
        }
    }
}
</script>

<style lang='less' scoped>
    .el-header{
        background-color:#212529;
        display:flex;
        justify-content: space-between;
        align-items: center;
        color:rgb(245, 245, 245);
        font-size:20px;
        padding-right: 50px;
        font-family:"Helvetica Neue",Helvetica,"PingFang SC","Hiragino Sans GB","Microsoft YaHei","微软雅黑",Arial,sans-serif;
    }
    .el-aside{
        background-color:#212529;
        .el-menu{
            border-right:0;
        }
    }
    .home-container{
        height: 100%;
        font-family: '微软雅黑';
    }
    .el-menu-item{
        margin-left: 10px;
    }
    .el-main{
        background-color:rgba(160, 160, 160, 0.219);
    }
    

</style>