<template>
    <div>
        <!--<head-top></head-top>-->
        <el-row style="margin-top: 20px;">
            <el-col :span="12" :offset="4">
                <el-form :model="formData" ref="formData" label-width="110px" class="demo-formData">
                    <el-form-item label="上传图片">
                        <el-upload
                            class="avatar-uploader"
                            :action="baseUrl + '/admin/upload/uploadImage'"
                            :show-file-list="false"
                            :on-success="handleShopAvatarScucess"
                            :before-upload="beforeAvatarUpload">
                            <img v-if="formData.image_path" :src="baseImgPath + formData.image_path" class="avatar">
                            <i v-else class="el-icon-plus avatar-uploader-icon"></i>
                        </el-upload>
                    </el-form-item>
                    <span>上传图片的访问地址为 {{ formData.http_image_url }}</span>
                </el-form>
            </el-col>
        </el-row>
    </div>
</template>

<script>
    import headTop from '@/components/headTop'
    import {cityGuess, addShop, searchplace, foodCategory} from '@/api/getData'
    import {baseUrl, baseImgPath} from '@/config/env'
    export default {
        data(){
            return {
                formData: {
                    image_path: '',
                    http_image_url: '',
                },
                baseUrl,
                baseImgPath,
            }
        },
        components: {
            headTop,
        },
        methods: {
            handleShopAvatarScucess(res, file) {
                if (res.code == 1) {
                    this.formData.http_image_url = "http://img.fanlidai.cc/" + res.obj;
                    this.formData.image_path =  "http://img.fanlidai.cc/" + res.image_path;
                    this.$message.success('图片上传成功！');
                }else{
                    this.$message.error('上传图片失败！');
                }
            },
            beforeAvatarUpload(file) {
                //const isRightType = (file.type === 'image/jpeg') || (file.type === 'image/png');
                const isLt2M = file.size / 1024 / 1024 < 2;

//                if (!isRightType) {
//                    this.$message.error('上传头像图片只能是 JPG 格式!');
//                }
                if (!isLt2M) {
                    this.$message.error('上传图片大小不能超过 2MB!');
                }
                return isLt2M;
            },
        }
    }
</script>

<style lang="less">
    @import '../style/mixin';
    .button_submit{
        text-align: center;
    }
    .avatar-uploader .el-upload {
        border: 1px dashed #d9d9d9;
        border-radius: 6px;
        cursor: pointer;
        position: relative;
        overflow: hidden;
    }
    .avatar-uploader .el-upload:hover {
        border-color: #20a0ff;
    }
    .avatar-uploader-icon {
        font-size: 28px;
        color: #8c939d;
        width: 120px;
        height: 120px;
        line-height: 120px;
        text-align: center;
    }
    .avatar {
        width: 120px;
        height: 120px;
        display: block;
    }
    .el-table .info-row {
        background: #c9e5f5;
    }

    .el-table .positive-row {
        background: #e2f0e4;
    }
</style>
