<template>
    <div class="section">
        <div class="section-top">
            <el-form>
                <el-button icon="el-icon-plus" style="background: #57AB33;border-color: #57AB33;color:#fff;" @click="addClick">创建</el-button>
                <el-form-item label="典籍库名称" style="width:300px;display: inline-block;margin-bottom:0;">
                    <el-input v-model="form.archiveLibName" clearable placeholder="请输入典籍库名称" style="width:200px;"></el-input>
                </el-form-item>
                <el-form-item label="作者" style="width:300px;display: inline-block;margin-bottom:0;">
                    <el-input v-model="form.author" clearable placeholder="请输入作者" style="width:200px;"></el-input>
                </el-form-item>
                <el-button icon="el-icon-search" @click="searchBtn" type="success">搜索</el-button>
            </el-form>
        </div>
        <div class="section-list">
            <template>
                <el-table :data="tableData" style="width: 100%">
                    <el-table-column prop="archiveLibName" label="典籍库名称" width="260" align="center"> </el-table-column>
                    <el-table-column prop="author" label="作者" width="200" align="center"> </el-table-column>
                    <el-table-column prop="publisher" label="出版社" width="290" align="center"> </el-table-column>
                    <el-table-column prop="publishTime" label="出版时间" width="160" align="center"> </el-table-column>
                    <el-table-column prop="operator" label="操作人" width="200" align="center"></el-table-column>
                    <el-table-column prop="operateTime" label="操作日期" width="160" align="center"></el-table-column>
                    <el-table-column label="操作" width="260" align="center">
                        <template slot-scope="scope">
                            <span @click="recordsShow(scope.row)" class="listBtn">进入典籍</span>
                            <span @click="viewShow(scope.row)" style="border-left:1px solid #ccc;" class="listBtn">查看</span>
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
import { list , del } from '@/api/manage/sample'
export default {
    created() {
        this.listShow()
    },
    methods:{
        recordsShow(row){
            this.$router.push({
                path: "/manage/sample/lists",
                query:{
                    id:row.id,
                    name:row.archiveLibName
                }
            }); 
        },
        delBtn(row){
            const h = this.$createElement;
            this.$msgbox({
                title: '消息',
                message: h('p', null, [
                    h('p', null, row.archiveLibName),
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
        viewShow(row){
            this.$router.push({
                path: "/manage/sample/view",
                query:{
                    id:row.id
                }
            }); 
        },
        editShow(row){
            this.$router.push({
                path: "/manage/sample/add",
                query:{
                    type:'UPDATE',
                    id:row.id
                }
            }); 
        },
        addClick(){
            this.$router.push({
                path: "/manage/sample/add",
                query:{
                    type:'ADD'
                }
            }); 
        },
        searchBtn(){
            this.form.page = 1
            this.listShow()
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

    data(){
        return{
            tableData:[],
            form:{
                page:1,
                rows:15,
                archiveLibName:'',
                author:''
            },
            total:0,
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