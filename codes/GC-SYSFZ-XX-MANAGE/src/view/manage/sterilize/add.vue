<template>
    <div class="section">
        <div class="section-bottom">
            <p class="view-title">批量导入图谱表</p>
            <el-form :model="form"  label-position="right" :rules="rules" ref="form" label-width="120px">
                <div class="add-div">
                    <!-- <p class="section-user">简介信息</p> -->
                    <div>
                        <el-form-item label="检测人" prop="inspectorId">
                            <el-select v-model="form.inspectorId" placeholder="请选择检测人" style="width:220px;">
                                <el-option v-for="item in inspectorIdOption" :key="item.itemId" :label="item.itemName" :value="item.itemId"></el-option>
                            </el-select>
                        </el-form-item>
                    </div>
                    <div>
                        <el-form-item label="检测日期" prop="inspectDate">
                            <el-date-picker value-format="yyyy-MM-dd" v-model="form.inspectDate" type="date" :picker-options="pickerOptions" placeholder="选择日期"></el-date-picker>
                        </el-form-item>
                    </div>
                    <div>
                        <el-form-item label="复核人" prop="reviewerId">
                            <el-select v-model="form.reviewerId" placeholder="请选择复核人" style="width:220px;">
                                <el-option v-for="item in reviewerIdOption" :key="item.itemId" :label="item.itemName" :value="item.itemId"></el-option>
                            </el-select>
                        </el-form-item>
                    </div>
                    <div>
                        <el-form-item label="复核日期" prop="reviewDate">
                            <el-date-picker value-format="yyyy-MM-dd" v-model="form.reviewDate" :picker-options="pickerOptionS" type="date" placeholder="选择日期"></el-date-picker>
                        </el-form-item>
                    </div>
                    <div class="item-name">
                        <el-form-item label="导入文件" prop="publishTime">
                            <div>
                                <div slot="tip" style="margin-bottom:10px;vertical-align: top;" class="el-upload__tips">(可以批量导入多个PDF文件，最多50个文件，单个文件大小≤10MB)</div>
                                <div class="video-right item-name">
                                    <el-upload
                                    class="upload-demo"
                                    name="file"
                                    :action="uploadUrl"
                                    :headers="uploadHeaders"
                                    :data="uploadImgData"
                                    :on-remove="handleRemove"
                                    :limit="50"
                                    multiple
                                    :on-success="handleAvatarSuccessPdf"
                                    :before-upload="beforeAvatarUploadPdf"
                                    :on-exceed="handleExceedMv"
                                    :file-list="fileList">
                                    <el-button size="small" type="primary">点击上传</el-button>
                                    </el-upload>
                                </div>
                            </div>
                        </el-form-item>
                    </div>
                </div>
                <div class="section-btn">
                    <div slot="footer" class="dialog-footer">
                        <el-button type="primary" style="width:200px;" @click="onSubmit('form')">确 定</el-button>
                        <el-button style="width:200px;" @click="resetForm('form')">取 消</el-button> 
                    </div>
                </div>
            </el-form>
        </div>
    </div>
</template>

<script>
import { add , users } from '@/api/manage/sterilize'
import { getToken } from "@/utils/auth";
export default {
    methods:{
        handleAvatarSuccessPdf(res, file ,fileList){
            this.setFormImgUrlByFileList(fileList,file);
            
        },
        setFormImgUrlByFileList(fileList,file) {
            this.form.files = []
            for(let x=fileList.length,i=0;i<x;i++){
                if(fileList[i].response.data){
                    this.form.files.push({
                        name:fileList[i].name,
                        url:fileList[i].response.data.url
                    })
                }
            }
        },
        handleExceedMv(files, pdf) {
            this.$message.warning(`当前限制选择 50 个文件`);
        },
        beforeAvatarUploadPdf(file) {
            this.loading = true
            let x = file.name.split(".")[1]
            const fileTypeValid = x === "pdf" ;
            if (!fileTypeValid) {
                this.$message.error("上传文件只能是 pdf 格式!");
                return false
            }
            const isLt2M = file.size / 1024 / 1024 < 10;
            if (!isLt2M) {
            this.$message.error("上传文件大小不能超过 10MB!");
            }
            return isLt2M;
        },
        handleRemove(file, fileList) {
            this.setFormImgUrlByFileList(fileList);
        },
        onSubmit(formName) {
            this.$refs[formName].validate((valid) => {
                if (valid) {
                    add(this.form).then(res =>{
                        if(res.status === '200'){
                            this.$message({
                                type: 'success',
                                message: '批量导入图谱表成功！'
                            });  
                            this.$router.push({
                                path: "/manage/sterilize/index"
                            }); 
                        }else{
                            this.$message({
                                type: 'error',
                                message: res.msg
                            });
                        }
                    })
                }else {
                    return false;
                }
            })
        },
        resetForm(){
            this.$router.push({
                path: "/manage/sterilize/index"
            }); 
        },
    },
    created(){
        users("D").then(res =>{
            if(res.status === '200'){
                this.inspectorIdOption = res.data
                for(let x=this.inspectorIdOption.length,i=0;i<x;i++){
                    if(this.inspectorIdOption[i].selected === true){
                        this.form.inspectorId = this.inspectorIdOption[i].itemId
                    }
                }
            }
        })
        users("R").then(res =>{
            if(res.status === '200'){
                this.reviewerIdOption = res.data
            }
        })
    },
    computed:{
        uploadUrl(){
            return process.env.BASE_API + "/upload/pic";
        },
        uploadHeaders() {
            return {
                ctoken: getToken()
            };
        },
    },
    data(){
        var reviewDate = (rule, value, callback) => {
            if(this.form.inspectDate === ""){
                this.form.reviewDate = ""
                return callback(new Error('请先选择检测日期'));
            }else if(value === ""){
                return callback(new Error('请先选择复核日期'));
            }else{
                callback()
            }
        }
        return{
            fileList:[],
            regionsOptions:[],
            uploadImgData:{
                type:"ATLAS_RECORD"
            },
            inspectorIdOption:[],
            reviewerIdOption:[],
            form:{
                files:[],
                inspectDate:"",
                inspectorId:"",
                reviewDate:"",
                reviewerId:"",
            },
            times:"",
            api:process.env.BASE_API,
            rules:{
                inspectorId:[
                    { required: true, message: '请选择检测人', trigger: 'change' },
                ],
                inspectDate:[
                    { required: true, message: '请选择检测日期', trigger: 'change' },
                ],
                reviewDate:[
                    // { required: true, message: '请选择复核日期', trigger: 'blur' },
                    {validator: reviewDate, trigger: 'change' },
                ],
                reviewerId:[
                    { required: true, message: '请选择复核人', trigger: 'change' },
                ]
            },
            pickerOptions: {
                disabledDate:(time) => {
                    this.times = time
                    return time.getTime() > Date.now();
                }
            },
            pickerOptionS: {
                disabledDate:(timeA) =>{
                    var dataTime = new Date(this.form.inspectDate)
                    return timeA.getTime() < dataTime.getTime();
                }
            },
        }
    }
}
</script>

<style scoped>
.el-upload__tips{
    font-size: 12px;
    color: #606266;
}
.viewDiv p{
    margin-bottom: 10px;
}
.viewDiv span{
    vertical-align: top;
}
.viewDiv{
    padding-left: 125px;
    font-size: 14px;
    color: #606266;
    margin-bottom: 10px;
}
.add-div{
    padding: 20px 50px;
    margin: 0 auto 0;
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
    margin: 20px 0 20px 20px;
    padding-left: 30px;
    width: 227px;
    height: 30px;
    line-height:30px;
    color: #333333;
}
.section-bottom{
    margin: 10px 0 0 10px;
    background: #fff;
    padding: 20px;
}
</style>