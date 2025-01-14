<template>
    <div class="section">
        <div class="section-top">
            <el-form>
                <el-button icon="el-icon-plus" style="background: #57AB33;border-color: #57AB33;color:#fff;" @click="addClick">创建</el-button>
                <el-form-item label="产品名称" style="width:300px;display: inline-block;margin-bottom:0;">
                    <el-input v-model="form.name" clearable placeholder="请输入产品名称" style="width:200px;"></el-input>
                </el-form-item>
                <el-form-item label="发布时间" style="width:450px;display: inline-block;margin-bottom:0;">
                    <el-date-picker v-model="timeData" clearable type="daterange" start-placeholder="开始日期" 
                        end-placeholder="结束日期" value-format="yyyy-MM-dd" format="yyyy-MM-dd">
                        </el-date-picker>  
                </el-form-item>
                <el-button icon="el-icon-search" @click="searchBtn" type="success">搜索</el-button>
            </el-form>
        </div>
        <div class="section-list">
            <template>
                <el-table :data="tableData" style="width: 100%">
                    <el-table-column prop="name" label="产品名称" width="350" align="center"> </el-table-column>
                    <el-table-column prop="model" label="产品型号" width="250" align="center"> </el-table-column>
                    <el-table-column prop="spec" label="产品规格" width="200" align="center"></el-table-column>
                    <el-table-column prop="operator" label="操作人" width="200" align="center"></el-table-column>
                    <el-table-column prop="operateDate" label="操作时间" width="200" align="center"></el-table-column>
                    <el-table-column label="操作" width="300" align="center">
                        <template slot-scope="scope">
                            <span @click="viewShow(scope.row)" class="listBtn">查看</span>
                            <span @click="editShow(scope.row)" style="border-left:1px solid #ccc;" class="listBtn">修改</span>
                            <span @click="delBtn(scope.row)" style="border-left:1px solid #ccc;" class="listBtn">删除</span>
                            <span @click="downloadBtn(scope.row)" style="border-left:1px solid #ccc;" class="listBtn">下载产品二维码</span>
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
import { list , del , Quick } from '@/api/product'
export default {
    created() {
        this.listShow()
    },
    methods:{
        downloadBtn(row){ 
            let name = row.name + '.png'
            Quick('download/'+ row.qrcode,name)
        },
        delBtn(row){
            const h = this.$createElement;
            this.$msgbox({
                title: '温馨提示',
                message: h('p', null, [
                    h('p', null, row.name),
                    h('p', null, "删除产品信息后，之前产品二维码将无法使用是否继续？")
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
                path: "/product/view",
                query:{
                    id:row.id
                }
            }); 
        },
        editShow(row){
            this.$router.push({
                path: "/product/add",
                query:{
                    type:'UPDATE',
                    id:row.id
                }
            }); 
        },
        addClick(){
            this.$router.push({
                path: "/product/add",
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
            if(this.timeData){
                this.form.startDate = this.timeData[0] 
                this.form.endDate = this.timeData[1] 
            }else{
                this.form.startDate = ''
                this.form.endDate = ''
            }
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
            timeData:[],
            tableData:[],
            form:{
                page:1,
                rows:15,
                name:'',
                startDate:'',
                endDate:""
            },
            total:0,
            api:process.env.BASE_API,
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