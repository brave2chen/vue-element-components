<template>
  <div class="app-export-demo">
    <AppExport name="导出" filename="template.csv" :types="types"></AppExport>
    <AppUpload name="导入" action="/template.csv" moduleFileUrl="/template.csv" moduleFileName="template.csv" accept=".csv" :size="1"></AppUpload>
  </div>
</template>
<script>
  // @ is an alias to /src
  import AppExport from '../components/AppExport'
  import AppUpload from '../components/AppUpload'

  export default {
    name: 'AppDemos',
    components: {
      AppExport, AppUpload
    },
    data(){
      return {
        types: [
          {label: "选项1", uri: '/template.csv', data: {ids: "1,2,3"}},
          {label: "选项2", uri: '/template.csv', data: {}},
        ]
      }
    }
  }
</script>