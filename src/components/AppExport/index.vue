<template>
  <div class="app-export el-button">
    <el-button type="primary" @click="visible=true">
      <slot>{{name}}</slot>
    </el-button>
    <el-dialog :visible.sync="visible" :title="name" center>
      <div class="app-export__main" v-loading="loading">
        <div><i class="app-export__icon el-icon-download"></i></div>
        <el-radio-group v-model="type">
          <el-radio v-for="(t,index) in types" :key="index" :label="index">{{t.label}}</el-radio>
        </el-radio-group>
        <el-divider></el-divider>
        <span class="app-export__btn">
          <el-button @click="visible=false">取消</el-button>
          <el-button type="primary" @click="exportData">导出</el-button>
        </span>
      </div>
    </el-dialog>
  </div>
</template>

<script>
  export default {
    name: 'AppExport',
    props: {
      // 标题名称
      name: {
        type: String,
        require: true,
      },
      // 默认导出类型的下标
      defaultType: {
        type: Number,
        default: 0,
      },
      // 导出前回调函数
      beforeExport: {
        type: Function,
      },
      // 导出类型数组[{label: ${导出类型名称}, uri: ${导出请求URL}, data: ${导出请求附加参数}},
      types: {
        type: Array,
        require: true,
      },
      // 导出文件名
      filename: {
        type: String,
        require: true,
      },
      // 导出请求方式，默认get
      method: {
        type: String,
        default: 'post',
      }
    },
    data() {
      return {
        visible: false,
        loading: false,
        type: this.defaultType,
      }
    },
    computed: {
      currentType() {
        return this.types[this.type];
      }
    },
    created() {
    },
    methods: {
      async exportData() {
        if (this.beforeExport && !this.beforeExport(this.currentType)) {
          return;
        }
        this.loading = true;
        // axios 添加download方法
        await this.$axios.download(this.currentType.uri, this.currentType.data, this.filename, {method: this.method})
        .catch(() => {
          this.loading = false;
          this.$message.error('文件下载失败')
          throw new Error('文件下载失败')
        })
        this.loading = false;
        this.visible = false;
      },
    }
  }
</script>

<style lang="scss" scoped>
  .app-export {
    display: inline-block;
    padding: 0;
    cursor: default;
  }

  .app-export__main {
    text-align: center;
  }

  .app-export__icon {
    font-size: 67px;
    color: #909399;
    margin: 0 0 30px 0;
    line-height: 50px;
    border: 1px solid #dcdfe6;
    padding: 15px;
    border-radius: 5px;
  }


</style>
