<template>
  <div class="home">
    <el-upload
      class="upload-demo"
      action
      :http-request="fnUploadRequest"
      list-type="picture-card"
      :on-preview="handlePictureCardPreview"
      :on-remove="handleRemove"
    >
      <i class="el-icon-plus"></i>
    </el-upload>
    <el-dialog :visible.sync="dialogVisible">
      <img width="100%" :src="dialogImageUrl" alt="" />
    </el-dialog>
    <!-- <el-upload
      class="upload-demo"
      action
      :http-request="fnUploadRequest"
      :on-preview="handlePreview"
      :on-remove="handleRemove"
      :file-list="fileList"
      list-type="picture"
    >
      <el-button size="small" type="primary">点击上传</el-button>
      <div slot="tip" class="el-upload__tip">
        只能上传jpg/png文件，且不超过500kb
      </div>
    </el-upload> -->
  </div>
</template>

<script>
// import { client } from "../utils/alioss";
import OSS from "ali-oss";
export default {
  props: ["id"],
  data() {
    return {
      dialogImageUrl: "",
      dialogVisible: false,
      disabled: false,
      selectImg: "",
      loading: false,
      arr: [],
      num: 0,
      value1: [],
      fileList: [],
      card: {
        id: 0,
        name: "",
        price: 0,
        desc: "",
        items: [],
        imgUrl: "",
      },
    };
  },
  methods: {
    handleRemove(file, fileList) {
      console.log(file, fileList);
    },
    handlePictureCardPreview(file) {
      this.dialogImageUrl = file.url;
      this.dialogVisible = true;
    },
    async fnUploadRequest(option) {
      try {
        console.log("参数", option);
        // 获取OSS配置信息
        let client = new OSS({
          secure: true,
          accessKeyId: "LTAI4FwwMhSouis1525cC2fP",
          accessKeySecret: "ACziMrcjplTvzstbZI1jjsAj16Dvm6",
          bucket: "jhao413",
          region: "oss-cn-beijing",
        });
        this.loading = true;
        function getFormatDate() {
          var date = new Date();
          var month = date.getMonth() + 1;
          var strDate = date.getDate();
          if (month >= 1 && month <= 9) {
            month = "0" + month;
          }
          if (strDate >= 0 && strDate <= 9) {
            strDate = "0" + strDate;
          }
          var currentDate =
            date.getFullYear() +
            "-" +
            month +
            "-" +
            strDate +
            " " +
            date.getHours() +
            ":" +
            date.getMinutes() +
            ":" +
            date.getSeconds();
          return currentDate;
        }

        let file = option.file;
        const point = file.name.lastIndexOf(".");
        let suffix = file.name.substr(point);
        let fileName = file.name.substr(0, point);
        let date = getFormatDate();
        let fileNames = `${fileName}_${date}${suffix}`;
        let relativePath = "blog/";
        console.log("oss客户端", client);
        console.log("文件", file);
        // 分片上传文件
        let ret = await client.multipartUpload(relativePath + fileNames, file, {
          progress: async function(p) {
            let e = {};
            e.percent = p * 100;
            option.onProgress(e);
          },
        });

        if (ret.res.statusCode === 200) {
          console.log("上传成功", ret);
        } else {
          option.onError("上传失败");
        }
      } catch (error) {
        console.error(error);
        option.onError("上传失败");
      }
      this.loading = false;
    },
  },
};
</script>

<style>
.home {
  width: 100%;
  height: 100vh;
  position: relative;
}

.upload-demo {
  width: 30%;
  height: 30%;
  position: absolute;
  top: 0;
  bottom: 0;
  right: 0;
  left: 0;
  margin: auto;
  text-align: center;
}
</style>
