<template>
    <div>
        <el-breadcrumb separator-class="el-icon-arrow-right">
            <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item>用户管理</el-breadcrumb-item>
            <el-breadcrumb-item>用户列表</el-breadcrumb-item> 
        </el-breadcrumb>
        <!-- 卡片视图 -->
        <el-card >
            <!-- 搜索 -->
            <el-row :gutter="20">
                <el-col :span="8">
                    <el-input placeholder="请输入用户名" class="input-with-select" v-model="queryInfo.query" clearable @clear="getUserList1">
                      <el-button slot="append" icon="el-icon-search" @click="getUserList1"></el-button>
                    </el-input>
                </el-col>
                <el-col :span="4">
                    <el-button type="primary" @click="adduserVisible = true">添加用户</el-button>
                </el-col>
            </el-row>
            <!-- 用户展示 -->
            <el-table :data="userlist" v-loading="loading">
                <el-table-column type="index"></el-table-column>
                <el-table-column label="用户名" prop="username"></el-table-column>
                <el-table-column label="电话" prop="phone"></el-table-column>
                <el-table-column label="注册日期" prop="date"></el-table-column>
                <el-table-column label="操作">
                    <!-- 按钮slot-scope="scope" -->
                    <template slot-scope="scope">
                        <!-- <el-tooltip class="item" effect="dark" content="编辑" placement="top" :enterable="false">
                            <el-button type="primary" size="mini"  icon="el-icon-edit" @click="showEditDialog(scope.row.username)"></el-button>
                        </el-tooltip> -->
                        <el-tooltip class="item" effect="dark" content="删除" placement="top" :enterable="false">
                            <el-button type="danger" size="mini"  icon="el-icon-delete" @click="removeUserByname(scope.row.username)"></el-button>
                        </el-tooltip>
                        
                    </template>
                </el-table-column>

            </el-table>
            <el-pagination
                @size-change="handleSizeChange"
                @current-change="handleCurrentChange"
                :current-page="queryInfo.pagenum"
                :page-sizes="[2, 5, 10, 15]"
                :page-size="queryInfo.pagesize"
                layout="total, sizes, prev, pager, next, jumper"
                :total="total">
            </el-pagination>
        </el-card>
        <!-- 添加用户 -->
        <el-dialog
        title="添加用户"
        :visible.sync="adduserVisible"
        width="30%"
        @close="addDialogClosed">
            <el-form :model="addForm" :rules="addFormRules" ref="addFormRef" label-width="80px">
                <el-form-item label="用户名" prop="username">
                    <el-input v-model="addForm.username" ></el-input>
                </el-form-item>
                <el-form-item label="密码" prop="password">
                    <el-input v-model="addForm.password" ></el-input>
                </el-form-item>
                <el-form-item label="确认密码" prop="cpassword">
                    <el-input v-model="addForm.cpassword" ></el-input>
                </el-form-item>
                <el-form-item label="电话" prop="phone">
                    <el-input v-model="addForm.phone" ></el-input>
                </el-form-item>
            </el-form>
            <!-- 底部按钮 -->
            <span slot="footer" class="dialog-footer">
                <el-button @click="adduserVisible = false">取 消</el-button>
                <el-button type="primary" @click="adduser">确 定</el-button>
            </span>
        </el-dialog>

        <!-- 编辑页面 -->
        <!-- <el-dialog
        title="修改用户信息"
        :visible.sync="edituserVisible"
        width="30%"
        :before-close="EditDialogClosed">
            
            <span slot="footer" class="dialog-footer">
                <el-button @click="edituserVisible = false">取 消</el-button>
                <el-button type="primary" @click="edituserVisible = false">确 定</el-button>
            </span>
        </el-dialog> -->
    </div>
</template>

<script>
export default {
    data() {
        var validatePass = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请再次输入密码'));
        } else if (value !== this.addForm.password) {
          callback(new Error('两次输入密码不一致!'));
        } else {
          callback();
        }
      };
        var checkMobile = (rule,value, callback) => {
            const regPhone = /^\d{6,11}$/
            if(regPhone.test(value)){
                return callback()
            }
            callback(new Error('请输入正确的手机号'))
        };
        return {
            queryInfo: {
                query: '',
                pagenum: 1,
                pagesize: 2
            },
            userlist: [
                {
                    username: "sss"

                }
            ],
            total: 0,
            loading: true,
            adduserVisible: false,
            edituserVisible:false,
            addForm: {
                username: '',
                password: '',
                cpassword: '',
                phone: '',
            },
            addFormRules: {
                username: [
                    {required: true, message: '请输入用户名', trigger: 'blur'},
                    { min: 3, max: 10, message: '长度在 3 到 10 个字符', trigger: 'blur' },
                ],
                password: [
                    { required: true, message: '请输入登录密码', trigger: 'blur' },
                    { min: 6, max: 15, message: '长度在 6 到 15 个字符', trigger: 'blur' },
                ],
                cpassword: [
                    {validator: validatePass, trigger: 'blur',required: true}
                ],
                phone: [
                    {required: true, message: '请输入电话号码', trigger: 'blur'},
                    {validator: checkMobile , trigger: 'blur' }
                ]
            },
        }
    },
    created() {
         this.getUserList()
    },
    methods: {
        async getUserList() {
            this.loading = true;
            const {data:res} = await this.$http.get('users',{ params: this.queryInfo})
            this.userlist = res.userlist;
            this.total = res.total;
            this.loading = false;
            console.log(res)
        },
        handleSizeChange(newSize){
            console.log(newSize)
            this.queryInfo.pagesize = newSize
            this.queryInfo.pagenum = 1
            this.getUserList()
        },
        handleCurrentChange(newPage){
            console.log(newPage)
            this.queryInfo.pagenum = newPage
            this.getUserList()
        },
        addDialogClosed(){
            this.$refs.addFormRef.resetFields();
        },
        adduser(){
            this.$refs.addFormRef.validate(async valid=>{
                if(!valid) return

               const {data: res} = await this.$http.post('users', this.addForm)

               if(res.success!== 'yes'){
                   this.$message.error("添加失败！")
               }else{
                   this.$message.success("添加用户成功！")
                   this.adduserVisible = false
                   this.getUserList1()
               }
            })
            
        },
        getUserList1(){
            this.queryInfo.pagenum=1
            this.getUserList()
        },
        async removeUserByname(name){
            // 确认confirm 取消cancel
         const confirmResult = await this.$confirm('此操作将永久删除该用户, 是否继续?', '提示', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
        }).catch(err => {
            return err
        })
        if(confirmResult !== 'confirm'){
            return this.$message.warning("已取消删除")
        }
        const {data:res} = await this.$http.get('deleteuser',{ params:{username: name}})

        if(res.success !== 'yes'){
             return this.$message.error("删除失败")
        }

        this.$message.warning("删除成功")
        this.getUserList1()
        }
    }
}
</script>

<style lang="less" scoped>

</style>