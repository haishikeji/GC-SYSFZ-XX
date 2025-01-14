<template>
    <div class="section">
        <div class="section-top">
            <el-form>
                <el-button icon="el-icon-plus" style="background: #57AB33;border-color: #57AB33;color:#fff;" @click="addClick">创建</el-button>
                <el-form-item label="检测依据" style="width:300px;display: inline-block;margin-bottom:0;">
                    <el-input v-model="form.checkBy" clearable placeholder="请输入检测依据" style="width:200px;"></el-input>
                </el-form-item>
                <el-button icon="el-icon-search" @click="searchBtn" type="success">搜索</el-button>
            </el-form>
        </div>
        <div class="section-list">
            <template>
                <el-table :data="tableData" style="width: 100%">
                    <el-table-column prop="checkBy" :show-overflow-tooltip="true" label="检测依据" width="300" align="center"> </el-table-column>
                    <el-table-column prop="sampleName" :show-overflow-tooltip="true" label="样品名称" width="300" align="center"> </el-table-column>
                    <el-table-column prop="measureToolNo" :show-overflow-tooltip="true" label="量具编号" width="200" align="center"> </el-table-column>
                    <el-table-column prop="curve" :show-overflow-tooltip="true" label="曲线" width="200" align="center">
                        <template slot-scope="scope">
                           <span v-if="scope.row.curve === 'Y'">基质曲线</span>
                           <span v-else>无</span>
                        </template>
                    </el-table-column>
                    <el-table-column prop="operator" :show-overflow-tooltip="true" label="编辑人" width="200" align="center"> </el-table-column>
                    <el-table-column prop="operateTime" :show-overflow-tooltip="true" label="更新日期" width="200" align="center"></el-table-column>
                    <el-table-column label="操作" width="150" align="center"> 
                        <template slot-scope="scope">
                            <span @click="editShow(scope.row)" class="listBtn">修改</span>
                            <span @click="delBtn(scope.row)" style="border-left:1px solid #ccc;" class="listBtn">删除</span>
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
                <el-form-item label="检测依据：" prop="checkBy">
                    <el-input placeholder="请输入检测依据" style="width: 250px;" v-model="popupForm.checkBy" clearable> </el-input>
                </el-form-item>
                <el-form-item label="样品名称：" prop="sampleName">
                    <el-input placeholder="请输入样品名称" style="width: 250px;" v-model="popupForm.sampleName" clearable> </el-input>
                </el-form-item>
                <el-form-item label="量具编号：" prop="measureToolId" style="width:500px;vertical-align: top;">
                    <el-select v-model="popupForm.measureToolId" @change="measureToolIdChange(popupForm.measureToolId)" placeholder="请选择" style="width: 250px;">
                        <el-option v-for="item in measureToolIdOption" :key="item.id" :label="item.item" :value="item.id"></el-option>
                    </el-select>
                </el-form-item>
                <el-form-item label="量具名称：" prop="name" style="width:300px;">
                    <p>{{name}}</p>
                </el-form-item>
                <el-form-item label="曲线：" prop="curve" style="width:300px;">
                    <template>
                        <el-radio v-model="popupForm.curve" label="Y">有基质曲线</el-radio>
                        <el-radio v-model="popupForm.curve" label="N">无基质曲线</el-radio>
                    </template>
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
import { list , add , update , measuretooldropdown , echo , del } from '@/api/BK'
import { resolve, reject } from 'q';
export default {
    methods:{
        measureToolIdChange(measureToolId){
            for(let x=this.measureToolIdOption.length,i=0;i<x;i++){
                if(measureToolId === this.measureToolIdOption[i].id){
                    if(this.measureToolIdOption[i].text === ""){
                        this.name = "无"
                    }else{
                        this.name = this.measureToolIdOption[i].text
                    }
                }   
            }
            
        },
        searchBtn(){
            this.form.page = 1
            this.listShow()
        },
        delBtn(row){
            const h = this.$createElement;
            this.$msgbox({
                title: '温馨提示',
                message: h('p', null, [
                    h('p', null, row.measureToolNo),
                    h('p', null, "删除后，将无法找回该信息，请确认！")
                ]),
                showCancelButton: true,
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                beforeClose: (action, instance, done) => {
                    if (action === 'confirm') {
                        del(row.id).then(res => {
                            if(res.status === '200'){
                                this.$message({type: 'success',message: '成功!'});
                                done()
                                this.listShow()
                            }else{
                                this.$message.error(res.msg)
                            }
                        })
                    } else {
                        done();
                    }
                }
            })
        },
        onSubmit(formName) {
            this.$refs[formName].validate((valid) => {
                if (valid) {
                    if(this.type === 'ADD'){
                        add(this.popupForm).then(res =>{
                            if(res.status === '200'){
                                this.$message({
                                    type: 'success',
                                    message: '新增量具成功！'
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
                                    message: '修改量具成功！'
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
            echo(row.id).then(res =>{
                if(res.status === '200'){
                    this.popupForm.checkBy = res.data.checkBy
                    this.popupForm.sampleName = res.data.sampleName
                    this.popupForm.measureToolId = res.data.measureToolId
                    this.popupForm.curve = res.data.curve
                    this.popupForm.id = res.data.id
                    this.dialogFormVisible = true
                    for(let x=this.measureToolIdOption.length,i=0;i<x;i++){
                        if(this.popupForm.measureToolId === this.measureToolIdOption[i].id){
                            if(this.measureToolIdOption[i].text === ""){
                                this.name = "无"
                            }else{
                                this.name = this.measureToolIdOption[i].text
                            }
                        }   
                    }
                }
            })
        },
        addClick(){
            this.type = "ADD"
            this.popupForm.checkBy = ""
            this.popupForm.sampleName = ""
            this.popupForm.measureToolId = ""
            this.popupForm.curve = ""
            this.popupForm.id = ""
            this.dialogFormVisible = true
        },
        listShow(){
            list(this.form).then(res =>{
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
        measuretooldropdown().then(res =>{
            if(res.status === "200"){
                this.measureToolIdOption = res.data
            }
        })
    },
    data(){
        var checkBy = (rule, value, callback) => {
             const reg = /^[\s\S]{1,30}$/
            if (!reg.test(value)) {
                return callback(new Error('支持1-30位的任意字符'));
            }else{
                callback()
            }
        }
        return{
            name:"",
            dialogFormVisible:false,
            measureToolIdOption:[],
            type:"ADD",
            title:"创建",
            tableData:[],
            form:{
                page:1,
                rows:15,
                checkBy:""
            },
            popupForm:{
                checkBy:"",
                sampleName:"",
                measureToolId:"",
                curve:"",
                id:""
            },
            total:0,
            rules:{
                checkBy:[
                    { required: true, message: '请输入检测依据', trigger: 'blur' },
                    { validator: checkBy, trigger: 'blur' }
                ],
                sampleName:[
                    { required: true, message: '请输入样品名称', trigger: 'blur' },
                    { validator: checkBy, trigger: 'blur' }
                ],
                measureToolId:[
                    { required: true, message: '请选择量具编号', trigger: 'blur' }
                ],
                curve:[
                    { required: true, message: '请选择曲线', trigger: 'blur' },
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