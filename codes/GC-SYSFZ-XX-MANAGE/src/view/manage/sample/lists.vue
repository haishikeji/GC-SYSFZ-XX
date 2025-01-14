<template>
    <div class="section">
        <div class="section-top">
            <el-form>
                <p class="view-title">{{name}}</p>
                <el-button icon="el-icon-plus" style="background: #57AB33;border-color: #57AB33;color:#fff;" @click="addClick">创建</el-button>
                <el-form-item label="药材名称" style="width:300px;display: inline-block;margin-bottom:0;">
                    <el-input v-model="form.crudeDrugs" clearable placeholder="请输入药材名称" style="width:200px;"></el-input>
                </el-form-item>
                <el-button icon="el-icon-search" @click="searchBtn" type="success">搜索</el-button>
            </el-form>
        </div>
        <div class="section-list">
            <template>
                <el-table :data="tableData" style="width: 100%">
                    <el-table-column prop="crudeDrugs" label="药材名称" width="300" align="center"> </el-table-column>
                    <el-table-column prop="operator" label="操作人" width="200" align="center"></el-table-column>
                    <el-table-column prop="operateTime" label="操作日期" width="180" align="center"></el-table-column>
                    <el-table-column label="操作" width="240" align="center">
                        <template slot-scope="scope">
                            <span @click="viewShow(scope.row)" class="listBtn">查看</span>
                            <span @click="editShow(scope.row)" style="border-left:1px solid #ccc;" class="listBtn">修改</span>
                            <span @click="delBtn(scope.row)" style="border-left:1px solid #ccc;" class="listBtn">删除</span>
                        </template>
                    </el-table-column>
                </el-table>
            </template>
            <div class="block">
                <el-pagination  @current-change="handleCurrentChange" :current-page="form.page"
                    :page-size="form.rows" layout="total,prev, pager, next, jumper" :total="total"> </el-pagination>
            </div>
        </div>
        
    </div>
</template>

<script>
import { lists , dels } from '@/api/manage/sample'
export default {
    created() {
        const params = this.$route.query;
        this.name = params.name
        this.form.archiveLibId = params.id
        this.listShow()
    },
    methods:{
        delBtn(row){
            const h = this.$createElement;
            this.$msgbox({
                title: '消息',
                message: h('p', null, [
                    h('p', null, row.crudeDrugs),
                    h('p', null, "删除后，将无法找回该信息，请确认！")
                ]),
                showCancelButton: true,
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                beforeClose: (action, instance, done) => {
                    if (action === 'confirm') {
                        dels(row.id).then(res => {
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
        viewShow(row){
            this.$router.push({
                path: "/manage/sample/views",
                query:{
                    archiveLibId:this.form.archiveLibId,
                    id:row.id,
                    name:this.name
                }
            }); 
        },
        editShow(row){
            this.$router.push({
                path: "/manage/sample/adds",
                query:{
                    type:'UPDATE',
                    id:row.id,
                    name:this.name
                }
            }); 
        },
        addClick(){
            this.$router.push({
                path: "/manage/sample/adds",
                query:{
                    type:'ADD',
                    archiveLibId:this.form.archiveLibId,
                    name:this.name
                }
            }); 
        },
        searchBtn(){
            this.form.page = 1
            this.listShow()
        },
        listShow(){
            lists(this.form).then(res =>{
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
    data(){
        return{
            
            name:'',
            tableData:[],
            form:{
                page:1,
                rows:15,
                crudeDrugs:'',
                archiveLibId:''
            },
            total:0,
        }
    }
}
</script>

<style scoped>
.view-title{
    margin-bottom: 10px;
    font-size:24px;
    font-weight:bold;
    line-height:41px;
    text-align: center;
}
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