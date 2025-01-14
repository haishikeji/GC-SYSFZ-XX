<template>
    <div class="section">
        <div class="section-top">
            <el-form>
                <el-button icon="el-icon-plus" style="background: #57AB33;border-color: #57AB33;color:#fff;" @click="addClick">创建</el-button>
            </el-form>
        </div>
        <div class="section-list">
            <template>
                <el-table :data="tableData" style="width: 100%">
                    <el-table-column prop="name" :show-overflow-tooltip="true" label="部门名称" width="400" align="center"> </el-table-column>
                    <el-table-column prop="creator" :show-overflow-tooltip="true" label="创建人" width="200" align="center"> </el-table-column>
                    <el-table-column prop="createDate" :show-overflow-tooltip="true" label="创建时间" width="300" align="center"></el-table-column>
                    <el-table-column label="操作" width="150" align="center"> 
                        <template slot-scope="scope">
                            <span @click="editShow(scope.row)"  class="listBtn">修改</span>
                        </template>
                    </el-table-column>
                </el-table> 
            </template>
            <div class="block">
                <el-pagination  @current-change="handleCurrentChange" :current-page="form.page"
                    :page-size="form.rows" layout="total,prev, pager, next" :total="total"> </el-pagination>
            </div>
        </div>
        <el-dialog :title="title" width="500px" :visible.sync="dialogFormVisible">
            <el-form :model="popupForm" label-position="right" :rules="rules" ref="popupForm" label-width="140px" >
                <el-form-item label="部门名称:" prop="name">
                    <el-input placeholder="请输入部门名称" style="width: 250px;" v-model="popupForm.name" clearable> </el-input>
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button type="primary" @click="onSubmit('popupForm')">确 定</el-button>
                <el-button @click="resetForm('popupForm')">取 消</el-button>
            </div>
        </el-dialog>
    </div>
</template>

<script>
import { userlist , add , update , validatecontactnumunique } from '@/api/basics/user'
// import { firstregion , subregion} from '@/api/dropdown'
import { resolve, reject } from 'q';
export default {
    methods:{
        onSubmit(formName) {
            this.$refs[formName].validate((valid) => {
                if (valid) {
                    if(this.type === 'ADD'){
                        add(this.popupForm).then(res =>{
                            if(res.status === '200'){
                                this.$message({
                                    type: 'success',
                                    message: '新增部门成功！'
                                }); 
                                this.dialogFormVisible = false 
                                this.listShow()
                            }else{
                                this.$message.error(res.msg); 
                            }
                        })
                    }else{
                        update(this.popupForm).then(res =>{
                            if(res.status === '200'){
                                this.$message({
                                    type: 'success',
                                    message: '修改部门成功！'
                                });
                                this.dialogFormVisible = false  
                                this.listShow()
                            }else{
                                this.$message.error(res.msg); 
                            }
                        })
                    }
                }else {
                    return false;
                }
            })
        },
        resetForm(){
            this.dialogFormVisible = false
        },
        editShow(row){
            this.type = "UPDATE"
            this.popupForm.name = row.name
            this.popupForm.id = row.id
            this.dialogFormVisible = true
        },
        addClick(){
            this.type = "ADD"
            this.popupForm.name = ""
            this.popupForm.id = ""
            this.dialogFormVisible = true
        },
        listShow(){
            userlist(this.form).then(res =>{
                if(res.status === '200'){
                    this.tableData = res.data.list
                    this.total = res.data.total 
                }
            })
        },
        handleCurrentChange(val) {
            this.form.page = val
            this.listShow()
        }
    },
    mounted(){
        this.listShow()
    },
    data(){
        var name = (rule, value, callback) => {
             const reg = /^[\s\S]{2,10}$/
            if (!reg.test(value)) {
                return callback(new Error('支持2-10位的任意字符'));
            }else{
                validatecontactnumunique(this.popupForm.id,this.popupForm.name).then(res => {
                    if (res.status === "200") {
                        if (res.data === true) {
                            callback()
                        } else {
                            return callback(new Error("该部门名称已存在"))
                        }
                    }else{
                        return callback(new Error(res.msg))
                    }
                })
            }
        }
        return{
            dialogFormVisible:false,
            type:"ADD",
            title:"创建部门",
            tableData:[],
            form:{
                page:1,
                rows:15,
            },
            popupForm:{
                name:"",
                id:""
            },
            total:0,
            rules:{
                name:[
                    { required: true, message: '请输入部门名称', trigger: 'blur' },
                    { validator: name, trigger: 'blur' }
                ]
            }
        }
    }
}
</script>

<style scoped> 

.complain-text{
    text-align: center;
}
.green{
    background: #67BDBA;
}
.blue{
    background: #799DBE;
}
.tableBtn{
    display: inline-block;
    margin-right:10px;
    padding: 2px 10px;
    border-radius: 4px;
    color: #fff;
    cursor: pointer;
}
.section-list{
    margin: 10px;
    background: #fff;
}
.section-top{
    margin: 10px;
    padding: 10px;
    background: #fff;
} 
</style>