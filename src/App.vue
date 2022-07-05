<template>
  <div id="app">
    <MinIOUpload ref="minio" :isdrag="isdrag" :initChunkUploadUrl="initChunkUploadUrl"  :composeFileUrl="composeFileUrl" :filechunkSize="filechunkSize" @success="success"></MinIOUpload>
    <el-table :data="tableData">
      <el-table-column label="文件名称" prop="fileName"></el-table-column>
      <el-table-column label="文件大小(字节)" prop="fileSize"></el-table-column>
      <el-table-column label="上传时间" prop="zEdittime"></el-table-column>
      <el-table-column label="操作">
           <template slot-scope="scope">
              <el-button type="text"  @click="download(scope.row)">下载</el-button>
           </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
import MinIOUpload from "./index.js";
import axios from "axios";
export default {
  name: "App",
  components: {
    MinIOUpload
  },
  data() {
    return {
      tableData:[],
      isdrag:true,
      //切片完获取切片上传地址
      initChunkUploadUrl:"http://192.168.2.191:8546/api/minio/minioFile/initChunkUpload",
      //拼合地址
      composeFileUrl:"http://192.168.2.191:8546/api/minio/minioFile/composeFile",
      // composeFileUrl:"http://192.168.9.35:9007/api/esAdmin/minio/composeFile",
      filechunkSize:1024 * 1024 * 10,
      getFileList:'http://192.168.2.191:8546/api/minio/minioFile/getFileList',
      getFileDownloadLink:'http://192.168.2.191:8546/api/minio/minioFile/getFileDownloadLink',
      }
      

  },
  created() {
  },
  mounted() {
    this.load()
  },
  methods: {
    success(val){
      console.log(val,"file")
      this.$refs.minio.changeStatus(val,"我尝试改状态文字")
      this.load();
    },
    load(){
        axios({
        url: this.getFileList,
        method: "post",
        headers: {
          "Content-Type": "application/json;charset=UTF-8",
        },
        data: {
          WebAPIBean: {
            query: {
            },
          },
        },
      }).then((res) => {
        this.tableData=res.data.data
      });
    },
    download(row){
        axios({
        url: this.getFileDownloadLink,
        method: "post",
        headers: {
          "Content-Type": "application/json;charset=UTF-8",
        },
        data: {
          WebAPIBean: {
            query: {md5:row.fileMd5,fileName:row.fileName
            },
          },
        },
      }).then((res) => {
        console.log(res)
        let filePath=res.data.data.filePath
        window.open(filePath)
      });
    }
  },
};
</script>


