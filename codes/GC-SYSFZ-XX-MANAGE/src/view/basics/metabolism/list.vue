<template>
    <div class="section">
        <div class="section-top">
            <el-form>
                <el-button icon="el-icon-plus" style="background: #57AB33;border-color: #57AB33;color:#fff;" @click="addClick">创建</el-button>
                <el-form-item label="SMILES"  style="width:300px;display: inline-block;margin-bottom:0;">
                    <el-input v-model="form.smiles" clearable placeholder="请输入SMILES" style="width:200px;"></el-input>
                </el-form-item>
                <el-form-item label="所属药材"  style="width:280px;display: inline-block;margin-bottom:0;">
                    <el-input v-model="form.crudeDrugs" clearable placeholder="请输入所属药材" style="width:200px;"></el-input>
                </el-form-item>
                <el-button icon="el-icon-search" @click="searchBtn" type="success">搜索</el-button>
            </el-form>
        </div>
        <div class="section-list">
            <template>
                <el-table :data="tableData" style="width: 100%">
                    <el-table-column prop="smiles" :show-overflow-tooltip="true" label="SMILES" width="400" align="center"> </el-table-column>
                    <el-table-column prop="crudeDrugs" :show-overflow-tooltip="true" label="所属药材" width="500" align="center"> </el-table-column>
                    <el-table-column prop="operator" :show-overflow-tooltip="true" label="创建人员" width="200" align="center"></el-table-column>
                    <el-table-column prop="operateTime" label="创建日期" width="200" align="center"></el-table-column>
                    <el-table-column label="操作" width="200" align="center"> 
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
import { list , del } from '@/api/basics/metabolism'
// import { firstregion , subregion} from '@/api/dropdown'
import { resolve, reject } from 'q';
export default {
    methods:{
        delBtn(row){
            const h = this.$createElement;
            this.$msgbox({
                title: '消息',
                message: h('p', null, [
                    h('p', null, row.smiles+'的代谢物信息'),
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
                path: '/basics/metabolism/view',
                query:{
                    id:row.id,
                }
            })
        },
        editShow(row){
            this.$router.push({
                path: '/basics/metabolism/add',
                query:{
                    id:row.id,
                    operType: 'UPDATE'
                }
            })
        },
        addClick(){
            this.$router.push({
                path: '/basics/metabolism/add',
                query: {
                    operType: 'ADD'
                }
            })
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
    mounted(){
        this.listShow()
    },
    data(){
        return{
            tableData:[],
            form:{
                page:1,
                rows:15,
                smiles:"",
                crudeDrugs:''
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