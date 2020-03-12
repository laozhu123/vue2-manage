<template>
    <div class="fillcontain">
        <head-top></head-top>
        <header class="admin_title">管理员信息</header>
        <div class="admin_set">
            <ul>
                <li>
                    <span>姓名：</span><span>{{adminInfo.user_name}}</span>
                </li>
                <li>
                    <span>注册时间：</span><span>{{adminInfo.create_time}}</span>
                </li>
                <li>
                    <span>管理员权限：</span><span>{{adminInfo.admin}}</span>
                </li>
                <li>
                    <span>管理员 ID：</span><span>{{adminInfo.id}}</span>
                </li>
                <li>
                    <span>更换头像：</span>
                    <el-upload
                      class="avatar-uploader"
                      :action="baseUrl + '/admin/update/avatar/' + adminInfo.id"
                      :show-file-list="false"
                      :on-success="uploadImg"
                      :before-upload="beforeImgUpload">
                      <img v-if="adminInfo.avatar" :src="baseImgPath + adminInfo.avatar" class="avatar">
                      <i v-else class="el-icon-plus avatar-uploader-icon"></i>
                    </el-upload>
                </li>    
            </ul>
        </div>
    </div>
</template>

<script>
	import headTop from '../components/headTop'
    import {mapState} from 'vuex'
    import {baseUrl, baseImgPath} from '@/config/env'
	import imageConversion from 'image-conversion'


    export default {
        data(){
            return {
                baseUrl,
                baseImgPath,
            }
        },
    	components: {
    		headTop,
    	},
        computed: {
            ...mapState(['adminInfo']),
        },
        methods: {
            uploadImg(res, file) {
                if (res.status == 1) {
                    this.adminInfo.avatar = res.image_path;
                }else{
                    this.$message.error('上传图片失败！');
                }
            },
            beforeImgUpload(file) {
                return new Promise((resolve, reject) => {
					if (file.size > 1024 * 40){
						imageConversion.compressAccurately(file, 40).then(res => { // console.log(res)
							var compressed_file = new File([res], file.name, {type: file.type, lastModified: Date.now()});
							compressed_file.uid = file.uid;
							resolve(compressed_file)
						})
					}else{
						resolve(file)
					}
				})
            },
        },
    }
</script>

<style lang="less">
	@import '../style/mixin';
	.explain_text{
		margin-top: 20px;
		text-align: center;
		font-size: 20px;
		color: #333;
	}
    .admin_set{
        width: 60%;
        background-color: #F9FAFC;
        min-height: 400px;
        margin: 20px auto 0;
        border-radius: 10px;
        ul > li{
            padding: 20px;
            span{
                color: #666;
            }
        }
    }
    .admin_title{
        margin-top: 20px;
        .sc(24px, #666);
        text-align: center;
    }
    .avatar-uploader .el-upload {
        border: 1px dashed #d9d9d9;
        margin-top: 10px;
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
</style>
