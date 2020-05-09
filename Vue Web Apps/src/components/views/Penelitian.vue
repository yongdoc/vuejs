<template>
  <div>
    <div id="spreadsheet">
    </div>
  </div>
</template>

<script>
import WebDataRocks from 'webdatarocks'
import 'webdatarocks/webdatarocks.css'
import axios from 'axios'

var hostname = 'localhost:8023'
// var hostname = '10.199.14.46:8023'
export default {
  name: 'App',
  mounted: function () {
    axios.get('http://' + hostname + '/api/penelitian/').then(res => {
      console.log(res.data)
      this.webdatarock = new WebDataRocks({
        container: '#spreadsheet',
        toolbar: true,
        report: {
          dataSource: {
            data: res.data
          },
          options: {
            grid: {
              type: 'flat',
              showTotals: false,
              showGrandTotals: false
            }
          }
        }
      })
    })
  }
}
</script>
