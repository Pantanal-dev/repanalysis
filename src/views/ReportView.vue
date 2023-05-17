<template>
  <div class="report">
    Report: {{ $route.params.companyName }}
  </div>
</template>

<script>
import "@/assets/styles/report.css"

export default {
  name: 'ReportView',
}
</script>