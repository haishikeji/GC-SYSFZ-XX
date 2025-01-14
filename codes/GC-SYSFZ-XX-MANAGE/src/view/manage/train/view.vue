<template>
    <div class="section">
        <div class="section-bottom">
            <p class="view-title">{{form.famousPrescriptionName}}</p>
            <p style="text-align:center;margin-bottom: 10px;">(请在医生指导下使用)</p>
            <el-form :model="form"  label-position="right" ref="form" label-width="120px">
                <div class="add-div">
                    <p class="section-user">基本信息</p>
                    <el-form-item label="所谓名医:" style="width:500px;margin-bottom:0;">
                        <span>{{String(form.physician)}}</span>
                    </el-form-item>
                    <el-form-item label="名方关键药材:" style="width:500px;margin-bottom:0;">
                        <span>{{String(form.crudeDrugs)}}</span>
                    </el-form-item>
                    <el-form-item label="名方内容:" style="width:500px;margin-bottom:0;">
                        <div v-html="form.content"></div>
                    </el-form-item>
                    <div class="section-btn">
                        <router-link to='/manage/train/index'>
                            <el-button  type="info" plain>返回</el-button>
                        </router-link>
                    </div>
                </div>
            </el-form>
        </div>
    </div>
</template>

<script>
import { view } from '@/api/manage/train'
export default {
    methods:{
        licenseImgClick(item){
            this.idImgUrl = process.env.BASE_API+ "download/" + item
            this.idImg2Visible = true
        },
    },
    created(){
        const params = this.$route.query;

        view(params.id).then(res =>{
            if(res.status === '200'){
                this.form = res.data
            }
        })

    },
    data(){
        return{
            form:{},
        }
    }
}
</script>

<style scoped>

.viewDiv p{
    margin-bottom: 10px;
}
.viewDiv span{
    vertical-align: top;
}
.viewDiv{
    padding-left: 80px;
    font-size: 14px;
    color: #606266;
    margin: 10px 0;
}
.add-div{
    padding: 20px 50px;
    margin: 0 auto;
    width:800px;
    background:rgba(255,255,255,1);
    border:1px solid rgba(233,233,233,1);
    box-shadow:0px 3px 10px rgba(0,0,0,0.16);
}
.view-title{
    margin-bottom: 10px;
    font-size:24px;
    font-weight:bold;
    line-height:41px;
    text-align: center;
}
.section{
    padding:20px 10px 0;
    width: 1540px;
    min-width: 1540px;
    min-height: 600px;
}
.section-code{
    background:url(../../../assets/index/view-code.png) no-repeat;
}
.section-user{
    background:url(../../../assets/index/view-basics.png) no-repeat;
}
.section-user,.section-code{
    margin: 20px 0 0 20px;
    padding-left: 30px;
    width: 227px;
    height: 30px;
    line-height:30px;
    color: #333333;
}
.section-bottom{
    margin-top: 10px;
    background: #fff;
    padding: 20px;
}
</style>