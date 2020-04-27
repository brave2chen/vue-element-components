<template>
  <div class="app-export-demo">
    <AppExport name="导出" filename="template.csv" :types="types" method="get"></AppExport>
  </div>
</template>
<script>
  // @ is an alias to /src
  import AppExport from '../components/AppExport'

  export default {
    name: 'AppExportDemo',
    components: {
      AppExport
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