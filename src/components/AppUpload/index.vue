<template>
  <div class="app-upload el-button">
    <el-button type="primary" @click="visible=true">
      <slot>{{name}}</slot>
    </el-button>
    <el-dialog :visible.sync="visible" :title="name" center>
      <div class="app-upload__main" v-loading="loading">
        <el-upload
            ref="upload"
            v-bind="$attrs"
            v-on="$listeners"
            drag
            :multiple="false"
            :show-file-list="false"
            :accept="accept"
            :before-upload="beforeUpload"
            :http-request="upload"
        >
          <i class="el-icon-upload"></i>
          <div class="app-upload__text">将文件拖到此处，或<em>点击上传</em></div>
        </el-upload>
        <el-divider></el-divider>
        <span class="app-upload__tip">
          <slot name="tip">
            只能上传{{accept}}文件，且不超过{{size}}M
            <template v-if="!!moduleFileUrl && !!moduleFileName">
              ，内容格式请参考：<el-link type="primary" @click="download">{{moduleFileName}}</el-link>
              <a ref="link" href="javascript:void(0)" v-show="false"></a>
            </template>
          </slot>
        </span>

      </div>
    </el-dialog>
  </div>
</template>

<script>
  export default {
    name: 'PUpload',
    props: {
      /* 除以下属性外：请参考el-upload组件，如上传文件附加请求参数：data */
      // 标题名称
      name: {
        type: String,
        require: true,
      },
      // 导入模板文件URL
      moduleFileUrl: {
        type: String,
        require: false,
      },
      // 导入模板文件名称
      moduleFileName: {
        type: String,
        require: false,
      },
      // 导入文件格式
      accept: {
        type: String,
        default: '.xlsx, .xls',
      },
      // 导入文件大小限制
      size: {
        Number,
        default: 10,
      },
      // 导入成功回调函数
      onSuccess: Function,
      // 导入失败回调函数
      onError: Function,
    },
    data() {
      return {
        visible: false,
        loading: false,
      }
    },
    methods: {
      beforeUpload(file) {
        if (this.$attrs['before-upload']) {
          return this.$attrs['before-upload']();
        }

        const isType = this.accept.split(',').some(type => file.name.includes(type.trim()))
        const isLt2M = file.size / 1024 / 1024 < this.size;
        if (!isType) {
          this.$message.error(`上传文件只能是 ${this.accept} 格式`);
        }
        if (!isLt2M) {
          this.$message.error(`上传头像图片大小不能超过 ${size} MB!`);
        }
        return isType && isLt2M;
      },
      async download() {
        // axios 添加download方法
        await this.$axios.download(this.currentType.uri, this.currentType.data, this.filename, {method: this.method})
          .catch(() => {
            this.loading = false;
            this.$message.error('模板文件下载失败')
            throw new Error('模板文件下载失败')
          })
      },
      upload({filename, file, action, data = {}}) {
        const formData = new FormData() // 创建一个对象实例
        formData.append(filename || 'file', file);
        Object.entries(data).forEach(([key, value]) => {
          value !== undefined && value !== null && formData.append(key, String(value))
        });
        const {code, msg} = this.$axios.post(action, formData, {
          headers: {
            "Content-Type": "multipart/form-data"
          },
        }).catch(e => {
          this.$message.error(msg)
          console.error(e)
          this.onError && this.onError(e)
        })
        if (code !== 200) {
          this.$message.error(msg)
          this.onError && this.onError()
        } else {
          this.visible = false
          this.onSuccess && this.onSuccess()
        }
      },
    }
  }
</script>

<style scoped>
  .app-upload {
    display: inline-block;
    padding: 0;
    cursor: default;
  }

  .app-upload__main {
    text-align: center;
  }

  .app-upload__tip {
    color: #909399;
  }
</style>
