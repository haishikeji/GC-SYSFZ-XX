<template>
    <div class="section">
        <div class="section-bottom">
            <p class="view-title">{{this.title}}新产品</p>
            <el-form :model="form"  label-position="right" :rules="rules" ref="form" label-width="120px">
                <div class="add-div">
                    <p class="section-user">基础信息</p>
                    <div>
                        <el-form-item label="产品名称" prop="name" style="width:500px;display: inline-block;vertical-align: top;">
                            <el-input v-model="form.name" clearable placeholder="请输入产品名称" style="width:350px;"></el-input>
                        </el-form-item>
                        <el-form-item label="产品型号" prop="model" style="width:500px;display: inline-block;vertical-align: top;">
                            <el-input v-model="form.model" clearable placeholder="请输入产品型号" style="width:350px;"></el-input>
                        </el-form-item>
                    </div>
                    <div>
                        <el-form-item label="产品规格" prop="spec" style="width:500px;display: inline-block;vertical-align: top;">
                            <el-input v-model="form.spec" clearable placeholder="请输入产品规格" style="width:350px;"></el-input>
                        </el-form-item>
                        <el-form-item label="存放条件" prop="storageCondition" style="width:500px;display: inline-block;vertical-align: top;">
                            <el-input v-model="form.storageCondition" clearable placeholder="请输入存放条件" style="width:350px;"></el-input>
                        </el-form-item>
                    </div>
                    <div>
                        <div style="width:500px;display: inline-block;vertical-align: top;">
                            <el-form-item label="保质期" prop="radio" style="width:500px;">
                                <template>
                                    <el-radio v-model="form.radio" label="0">无保质期</el-radio>
                                    <el-radio v-model="form.radio" label="1">有保质期</el-radio>
                                </template>
                            </el-form-item>
                            <el-form-item v-if="form.radio === '1'" label="" prop="shelfLife" style="width:500px;">
                                <el-input v-model="form.shelfLife" clearable placeholder="请输入保质期" style="width:300px;"></el-input>
                                <span> 个月</span>
                            </el-form-item>
                        </div>
                        
                        <el-form-item label="应用场景" prop="useScenario" style="width:500px;display: inline-block;vertical-align: top;">
                            <el-input type="textarea" show-word-limit v-model="form.useScenario" maxlength="100" rows='5' clearable placeholder="请输入应用场景" style="width:350px;"></el-input>
                        </el-form-item>
                    </div>
                    <div>
                        <el-form-item label="主图(产品正面图):" prop="mainImg">
                            <p style="color: #606266;font-size:12px;margin:0 0 5px 5px;">仅支持JPG格式，图片大小不超过1MB</p>
                                <el-upload
                                :action="uploadUrl"
                                :on-success="handleUploadSuccess1"
                                name="file"
                                :headers="uploadHeaders"
                                :limit="1"
                                :before-upload="beforeUpload"
                                list-type="picture-card"
                                :file-list="fileList1"
                                :on-preview="handlePictureCardPreview"
                                :class="{cartshow:hideBox1}"
                                :data="uploadImgData1"
                                :on-remove="handleRemove1">
                                <i class="el-icon-plus"></i>
                                </el-upload>
                        </el-form-item>
                    </div>
                    <div>
                        <el-form-item label="产品图片详情或产品侧面图:" prop="detailsImg">
                            <p style="color: #606266;font-size:12px;margin:0 0 5px 5px;">仅支持1-8张JPG格式，图片大小不超过1MB</p>
                                <el-upload
                                :action="uploadUrl"
                                :on-success="handleUploadSuccess2"
                                name="file"
                                :headers="uploadHeaders"
                                :limit="8"
                                :before-upload="beforeUpload"
                                list-type="picture-card"
                                :file-list="fileList2"
                                :on-preview="handlePictureCardPreview"
                                :class="{cartshow:hideBox2}"
                                :data="uploadImgData2"
                                :on-remove="handleRemove2">
                                <i class="el-icon-plus"></i>
                                </el-upload>
                        </el-form-item>
                    </div>
                    <div style="margin:20px;width:500px;display: inline-block;vertical-align: top;">
                        <el-form-item label="产品pdf说明书:" prop="pdfManual" style="width:420px;">
                            <p style="color: #606266;font-size:12px;margin:0 0 5px 5px;">(仅支持1个PDF格式文件，大小不超过10MB)</p>
                            <div>
                                <div class="video-left">
                                    <i class="el-icon-document" style="color:#409EFF;"></i>
                                </div>
                                <div class="video-right item-name">
                                    <el-upload
                                    class="upload-demo"
                                    name="file"
                                    :action="uploadUrl"
                                    :on-success="handleAvatarSuccessPdf"
                                    :limit="1"
                                    accept=".pdf"
                                    :before-upload="beforeAvatarUploadPdf"
                                    :on-exceed="handleExceedMv"
                                    :data="uploadDataPdf"
                                    :on-remove="handleRemove3"
                                    :file-list="pdf">
                                    <el-button v-if="!form.pdfManual" size="small" type="primary">点击上传</el-button>
                                    <el-button v-else slot="tip" size="small" disabled type="primary">点击上传</el-button>
                                    </el-upload>
                                </div>
                            </div>
                        </el-form-item>
                    </div>
                    <div style="margin:20px;width:500px;display: inline-block;vertical-align: top;">
                        <el-form-item label="产品视频简介:" prop="videoUrl" style="width:420px;">
                            <p style="color: #606266;font-size:12px;margin:0 0 5px 5px;">(仅支持1个MP4的视频文件，大小不超过30M)</p>
                            <div>
                                <div class="video-left">
                                    <i class="el-icon-video-camera" style="color:#409EFF;"></i>
                                </div>
                                <div class="video-right item-name">
                                    <el-upload
                                    class="upload-demo"
                                    name="file"
                                    :action="uploadUrl"
                                    :on-success="handleAvatarSuccessMv"
                                    :limit="1"
                                    :before-upload="beforeAvatarUploadMv"
                                    :on-exceed="handleExceedMv"
                                    :data="uploadDataMv"
                                    :on-remove="handleRemove4"
                                    :file-list="video">
                                    <el-button v-if="!form.videoUrl" size="small" type="primary">点击上传</el-button>
                                    <el-button v-else slot="tip" size="small" disabled type="primary">点击上传</el-button>
                                    </el-upload>
                                </div>
                            </div>
                        </el-form-item>
                    </div>
                    <el-dialog :visible.sync="dialogVisible">
                        <img width="100%" :src="dialogImageUrl" alt="">
                    </el-dialog>
                    <div class="section-btn">
                        <div slot="footer" class="dialog-footer">
                            <el-button type="primary" @click="onSubmit('form')">确 定</el-button>
                            <el-button @click="resetForm('form')">取 消</el-button> 
                        </div>
                    </div>
                </div>
            </el-form>
        </div>
    </div>
</template>

<script>
import { echo , add , update , validateName } from '@/api/product'
import { getToken } from "@/utils/auth";
export default {
    methods:{
        beforeAvatarUploadPdf(file) {
            const isLt2M = file.size / 1024 / 1024 < 10;
            if (!isLt2M) {
            this.$message.error("上传文件大小不能超过 10MB!");
            }
            return isLt2M;
        },
        handleAvatarSuccessPdf(res, file ,fileList){
            this.form.videoUrl = "";
            this.form.videoName = ""
            for(let x=fileList.length,i=0;i<x;i++){
                this.form.pdfManual = fileList[i].response.data
                this.form.pdfManualName = fileList[i].name
            }
        },
        handleRemove3(){
            this.form.pdfManual = "";
            this.form.pdfManualName = ""
        },
        handleRemove4(){
            this.form.videoUrl = "";
            this.form.videoName = ""
        },
        handleExceedMv(files, pdf) {
            this.$message.warning(`当前限制选择 1 个文件`);
        },
        beforeAvatarUploadMv(file) {
            const fileTypeValid = file.type === "video/rm" || file.type === "video/rmvb" || file.type === "video/mp4" ;
            const fileSizeValid = file.size / 1024 / 1024 < 30;
            if (!fileTypeValid) {
                this.$message.error("上传视频只能是 RM/RMVB/MP4 格式!");
                return false
            }
            if (!fileSizeValid) {
                this.$message.error("上传文件大小不能超过30M!");
                return fileSizeValid;
            }else{
                return true
            }
        },
        handleAvatarSuccessMv(res, file ,fileList){
            this.form.videoUrl = fileList[0].response.data;
            this.form.videoName = fileList[0].name
        },
        handleRemove2(file, fileList) {
            this.setFormImgUrlByFileList(fileList);
        },
        setFormImgUrlByFileList(fileList) {
            this.form.detailsImg = []
            for(let x=fileList.length,i=0;i<x;i++){
                if(fileList[i].url.startsWith("blob")){
                    this.form.detailsImg.push(this.replaceImgUrl(fileList[i].response.data))
                }else{
                    this.form.detailsImg.push(this.replaceImgUrl(fileList[i].url))
                }
            }
        },
        handleUploadSuccess2(response, file, fileList) {
            if (response.status === "200") {
                this.setFormImgUrlByFileList(fileList);
            }
        },
        handleRemove1(file, fileList) {
            this.form.mainImg = ""
        },
        handlePictureCardPreview(file) {
            this.dialogImageUrl = file.url;
            this.dialogVisible = true;
        },
        beforeUpload(file) {
            const fileTypeValid = file.type === "image/jpeg" || file.type === "image/png" ;
            const fileSizeValid = file.size / 1024 / 1024 < 1;
            if (!fileTypeValid) {
                this.$message.error("上传图片只能是 JPG/JEPG/PNG 格式!");
                return false
            }
            if (!fileSizeValid) {
                this.$message.error("上传图片大小不能超过 1MB!");
                return false
            }
            return true
        },
        handleUploadSuccess1(response, file, fileList) {
            if (response.status === "200") {
                this.form.mainImg = ""
                if(fileList[0].url.startsWith("blob")){
                    this.form.mainImg = this.replaceImgUrl(fileList[0].response.data)
                }else{
                    this.form.mainImg = this.replaceImgUrl(fileList[0].url)
                }
            }
        },
        replaceImgUrl(url){
            if(url){
                return url.replace(process.env.BASE_API + "download/",'')
            }
        },
        resetForm(){
            this.$router.push({
                path: "/product/index"
            }); 
        },
        onSubmit(formName) {
            this.$refs[formName].validate((valid) => {
                if (valid) {
                    if(this.form.radio === "0"){
                        this.form.shelfLife = 0
                    }
                    if(this.type === 'ADD'){
                        add(this.form).then(res =>{
                            if(res.status === '200'){
                                this.$message({
                                    type: 'success',
                                    message: '创建成功！'
                                });  
                                this.$router.push({
                                    path: "/product/index"
                                }); 
                            }
                        })
                    }else{
                        update(this.form).then(res =>{
                            if(res.status === '200'){
                                this.$message({
                                    type: 'success',
                                    message: '修改成功！'
                                });  
                                this.$router.push({
                                    path: "/product/index"
                                });
                            }
                        })
                    }
                }else {
                    return false;
                }
            })
        },
    },
    created(){
        const params = this.$route.query;
        this.type = params.type
        if(this.type === 'ADD'){
            this.title = '新建'
        }else{
            this.title = '修改'
            echo(params.id).then(res =>{
                if(res.status === '200'){
                    this.form.id = res.data.id
                    this.form.model = res.data.model
                    this.form.name = res.data.name
                    this.form.spec = res.data.spec
                    this.form.storageCondition = res.data.storageCondition
                    this.form.useScenario = res.data.useScenario
                    if(res.data.shelfLife === 0){
                        this.form.radio = "0"
                    }else{
                        this.form.radio = "1"
                        this.form.shelfLife = res.data.shelfLife
                    }
                    if(res.data.mainImg != ""){
                        this.form.mainImg = res.data.mainImg
                        this.fileList1.push({url:process.env.BASE_API+'download/' + res.data.mainImg})
                    }
                    if(res.data.detailsImg.length != 0){
                        for(let x=res.data.detailsImg.length,i=0;i<x;i++){
                            this.form.detailsImg.push(res.data.detailsImg[i])
                            this.fileList2.push({url:process.env.BASE_API+'download/' + res.data.detailsImg[i]})
                        }
                    }
                    if(res.data.pdfManual != ""){
                        this.form.pdfManual = res.data.pdfManual
                        this.form.pdfManualName = res.data.pdfManualName
                        this.pdf.push({
                            name:res.data.pdfManualName,
                            url:process.env.BASE_API+'download/' + res.data.pdfManual
                        })
                    }
                    if(res.data.videoUrl != ""){
                        this.form.videoUrl = res.data.videoUrl
                        this.form.videoName = res.data.videoName
                        this.video.push({
                            name:res.data.videoName,
                            url:process.env.BASE_API+'download/' + res.data.videoUrl
                        })
                    }
                }
            })
        }
        
    },
    computed:{
        uploadUrl(){
            return process.env.BASE_API+"/upload/file"
        },
        uploadHeaders() {
            return {
                ctoken: getToken()
            };
        },
        hideBox1(){
            return this.form.mainImg != ""
        },
        hideBox2(){
            return this.form.detailsImg.length >= 8
        }
    },
    data(){
        var name = (rule, value, callback) => {
            const reg = /^[\s\S]{2,20}$/
            if (!reg.test(value)) {
                return callback(new Error('限制长度2-20位，支持任意字符'));
            }else{
                validateName(this.form.id,this.form.name).then(res =>{
                    if (res.status === "200") {
                        if (res.data === true) {
                            callback()
                        } else {
                            return callback(new Error("该产品名称已存在"))
                        }
                    }else{
                        return callback(new Error(res.msg))
                    }
                })
            }
        }
        var model = (rule, value, callback) => {
            const reg = /^[\s\S]{2,20}$/
            if (!reg.test(value)) {
                return callback(new Error('限制长度2-20位，支持任意字符'));
            }else{
                callback()
            }
        }
        var shelfLife = (rule, value, callback) => {
            const reg = /^[0-9]{1,4}$/
            if(value){
                if (!reg.test(value)) {
                    return callback(new Error('仅支持0＜X≤1000的正整数'));
                }else{
                    if(value<=0 || value > 1000 ){
                        return callback(new Error('仅支持0＜X≤1000的正整数'));
                    }else{
                        callback()
                    }
                }
            }else{
                callback()
            }
        }
        var storageCondition = (rule, value, callback) => {
            const reg = /^[\s\S]{2,50}$/
            if(value){
                if (!reg.test(value)) {
                    return callback(new Error('限制长度2-50位，支持任意字符'));
                }else{
                    callback()
                }
            }else{
                callback()
            }
        }
        return{
            uploadImgData1:{
                type:'PRODUCT_MAIN'
            },
            uploadImgData2:{
                type:'PRODUCT_DETAILS'
            },
            uploadDataPdf:{
                type:'PRODUCT_MANUAL'
            },
            uploadDataMv:{
                type:'PRODUCT_VIDEO'
            },
            fileList1:[],
            fileList2:[],
            pdf:[],
            video:[],
            dialogVisible:false,
            dialogImageUrl:"",
            type:"ADD",
            form:{
                detailsImg:[],
                id:'',
                mainImg:'',
                model:"",
                name:'',
                pdfManual:"",
                pdfManualName:"",
                shelfLife:"",
                spec:"",
                storageCondition:'',
                useScenario:'',
                videoName:'',
                videoUrl:'',
                radio:"0"
            },
            api:process.env.BASE_API,
            title:'新增',
            rules:{
                mainImg:[
                    { required: true, message: '请上传主图', trigger: 'change' }
                ],
                model:[
                    { required: true, message: '产品型号不能为空', trigger: 'blur' },
                    { validator: model, trigger: 'blur' }
                ],
                name:[
                    { required: true, message: '产品名称不能为空', trigger: 'blur' },
                    { validator: name, trigger: 'blur' }
                ],
                spec:[
                    { required: true, message: '产品规格不能为空', trigger: 'blur' },
                    { validator: model, trigger: 'blur' }
                ],
                detailsImg:[
                    { required: true, message: '请上传详情图', trigger: 'change' },
                ],
                shelfLife:[
                    { validator: shelfLife, trigger: 'blur' }
                ],
                storageCondition:[
                    { validator: storageCondition, trigger: 'blur' }
                ],
                useScenario:[
                    { required: true, message: '应用场景不能为空', trigger: 'blur' },
                ],
                // videoUrl:[
                //     { required: true, message: '请上传产品视频简介', trigger: 'blur' },
                // ],
                radio:[
                    { required: true, message: '请选择保质期', trigger: 'blur' },
                ]
            }
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
    padding-left: 125px;
    font-size: 14px;
    color: #606266;
    margin-bottom: 10px;
}
.add-div{
    padding: 20px 50px;
    margin: 0 auto;
    width:1100px;
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
    background:url(../../assets/index/view-code.png) no-repeat;
}
.section-user{
    background:url(../../assets/index/view-basics.png) no-repeat;
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