<template>
    <div>
        <el-breadcrumb separator-class="el-icon-arrow-right">
            <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item>订单管理</el-breadcrumb-item>
            <el-breadcrumb-item>订单列表</el-breadcrumb-item> 
        </el-breadcrumb>
        <!-- 卡片视图 -->
        <el-card >
            <!-- 搜索 -->
            <el-row :gutter="20">
                <el-col :span="8">
                    <el-input placeholder="请输入商品名称" class="input-with-select" v-model="queryInfo.query" clearable @clear="getOrderList1">
                      <el-button slot="append" icon="el-icon-search" @click="getOrderList1"></el-button>
                    </el-input>
                </el-col>
                <el-col :span="4">
                    <el-button type="primary" @click="addOrderVisible = true">添加订单</el-button>
                </el-col>
            </el-row>
            <!-- 订单展示 -->
            <el-table :data="orderlist" v-loading="loading">
                <el-table-column type="index"></el-table-column>
                <el-table-column label="订单号" prop="orderid"></el-table-column>
                <el-table-column label="订单名称" prop="ordername"></el-table-column>
                <el-table-column label="价格" prop="price"></el-table-column>
                <el-table-column label="时间" prop="time"></el-table-column>
                <el-table-column label="状态" prop="state">
                    <template slot-scope="scope">
                        <el-switch
                            v-model="scope.row.state"
                            active-color="#13ce66"
                            inactive-color="#ff4949"
                            @change="changestate(scope.row)">
                        </el-switch>
                    </template>
                </el-table-column>
                <el-table-column label="操作">
                    <!-- 按钮slot-scope="scope" -->
                    <template slot-scope="scope">
                        <el-tooltip class="item" effect="dark" content="编辑" placement="top" :enterable="false">
                            <el-button type="primary" size="mini"  icon="el-icon-edit" @click="showEditDialog(scope.row)"></el-button>
                        </el-tooltip>
                        <el-tooltip class="item" effect="dark" content="删除" placement="top" :enterable="false">
                            <el-button type="danger" size="mini"  icon="el-icon-delete" @click="removeOrderByid(scope.row.orderid)"></el-button>
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
        <!-- 添加订单 -->
        <el-dialog
        title="新增订单"
        :visible.sync="addOrderVisible"
        width="30%"
        @close="addDialogClosed">
            <el-form :model="addForm" :rules="addFormRules" ref="addFormRef" label-width="80px">
                <el-form-item label="订单id" prop="orderid">
                    <el-input v-model="addForm.orderid" ></el-input>
                </el-form-item>
                <el-form-item label="订单名称" prop="ordername">
                    <el-input v-model="addForm.ordername" ></el-input>
                </el-form-item>
                <el-form-item label="价格" prop="price">
                    <el-input-number v-model="addForm.price" :precision="2" :step="100" :max="10000000.00" :min="0.00"></el-input-number>
                </el-form-item>
                <el-form-item label="状态" prop="state">
                    
                        <el-switch
                            v-model="addForm.state"
                            active-color="#13ce66"
                            inactive-color="#ff4949">
                        </el-switch>
                    
                </el-form-item>
                

            </el-form>
            <!-- 底部按钮 -->
            <span slot="footer" class="dialog-footer">
                <el-button @click="addOrderVisible = false">取 消</el-button>
                <el-button type="primary" @click="addorder">确 定</el-button>
            </span>
        </el-dialog>

        <!-- 编辑页面 -->
         <el-dialog
        title="修改订单信息"
        :visible.sync="editOrderVisible"
        width="30%"
        :before-close="EditDialogClosed">
            <el-form :model="editForm" ref="editFormRef" label-width="80px">
                <el-form-item label="订单id" prop="orderid" >
                    <el-input v-model="editForm.orderid" :disabled="true"></el-input>
                </el-form-item>
                <el-form-item label="订单名称" prop="ordername" >
                    <el-input v-model="editForm.ordername" :disabled="true"></el-input>
                </el-form-item>
                <el-form-item label="订单日期" prop="time" >
                    <el-input v-model="editForm.time" :disabled="true"></el-input>
                </el-form-item>
                <el-form-item label="价格" prop="price">
                    <el-input-number v-model="editForm.price" :precision="2" :step="100" :max="10000000.00" :min="0.00"></el-input-number>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="editOrderVisible = false">取 消</el-button>
                <el-button type="primary" @click="editOrder">确 定</el-button>
            </span>
        </el-dialog>
    </div>
</template>

<script>
export default{
    data() {
        var checkId = (rule,value, callback) => {
            const regId = /^[1-9]([0-9]{6})$/
            if(regId.test(value)){
                return callback()
            }
            callback(new Error('ID必须为七位数字'))
        };
        return{
            queryInfo: {
                query: '',
                pagenum: 1,
                pagesize: 2
            },
            orderlist: [
                {
                    orderid: "123456",
                    ordername: "ssss",
                    price: "200",
                    time: "12-5-4",
                    state: true 
                }
            ],
            total: 0,
            loading: true,
            addOrderVisible: false,
            editOrderVisible:false,
            addForm: {
                orderid: 1000000,
                    ordername: "",
                    price: 100.00,
                    time: "",
                    state: true , 
            },
            editForm: {
                orderid: 1000000,
                    ordername: "",
                    price: 100.00,
                    time: "",
                    state: true , 
            },
            //^[1-9]\d*$
            addFormRules: {
                ordername: [
                    {required: true, message: '请输入订单名称', trigger: 'blur'},
                    { min: 3, max: 20, message: '长度在 3 到 20 个字符', trigger: 'blur' },
                ],
                orderid: [
                    {validator: checkId , trigger: 'blur' },
                    { required: true, message: '请输入七位订单号', trigger: 'blur' },
                    
                ],
            },
            showEditDialog(row){
                this.editForm = row;
                this.editOrderVisible = true;
            }
        }
    },
    created() {
        this.getOrderList()
    },
    methods: {
        async getOrderList() {
            this.loading = true;
            const {data:res} = await this.$http.get('orders',{ params: this.queryInfo})
            this.orderlist = res.orderlist;
            this.total = res.total;
            this.loading = false;
            console.log(res)
        },
        handleSizeChange(newSize){
            console.log(newSize)
            this.queryInfo.pagesize = newSize
            this.queryInfo.pagenum = 1
            this.getOrderList()
        },
        handleCurrentChange(newPage){
            console.log(newPage)
            this.queryInfo.pagenum = newPage
            this.getOrderList()
        },
        addDialogClosed(){
            this.$refs.addFormRef.resetFields();
        },
        addorder(){
            this.$refs.addFormRef.validate(async valid=>{
                if(!valid) return

               const {data: res} = await this.$http.post('orders', this.addForm)

               if(res.success!== 'yes'){
                   if(res.success === 'id' ){this.$message.error("订单id已存在！")}else{
                       this.$message.error("添加失败！")
                   }
                   
               }else{
                   this.$message.success("添加成功！")
                   this.addOrderVisible = false
                   this.getOrderList1()
               }
            })
            
        },
        async editOrder(){
            const {data:res} = await this.$http.post('orderchange', this.editForm)
            if(res.success!== 'yes'){
                    this.$message.error("编辑失败！")
               }else{
                    this.$message.success("编辑成功！")
                    this.editOrderVisible = false
                    this.getOrderList()
               }

        },
        async changestate(row){
            const {data:res} = await this.$http.get('orderchange',{ params: {orderid:row.orderid,state:row.state}})
            this.getOrderList()
        },
        getOrderList1(){
            this.queryInfo.pagenum=1
            this.getOrderList()
        },
        async removeOrderByid(id){
            // 确认confirm 取消cancel
        const confirmResult = await this.$confirm('此操作将永久删除该订单, 是否继续?', '提示', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
        }).catch(err => {
            return err
        })
        if(confirmResult !== 'confirm'){
            return this.$message.warning("已取消删除")
        }
        const {data:res} = await this.$http.get('deleteorder',{ params:{orderid: id}})

        if(res.success !== 'yes'){
             return this.$message.error("删除失败")
        }

        this.$message.warning("删除成功")
        this.getOrderList1()
        },

    },

}
</script>

<style lang="less" scoped>

</style>
