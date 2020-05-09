<template>
  <div>
    <div id="app" ref="spreadsheet">
      <input type="button" value="Add id" @click="() => spreadsheet.insertRow()"/>
      <input type="button" value="Delete id" @click="() => spreadsheet.deleteRow()"/>
    </div>
  </div>
</template>

<script>
import jexcel from 'jexcel'
import 'jexcel/dist/jexcel.css'
import axios from 'axios'

var hostname = 'localhost:8023'
// var hostname = '10.199.14.46:8023'
var update = function(instance, cell, x, y, val) {
  axios
    .get('http://' + hostname + '/api/datadasar/')
    .then((response) => {
      var index = Object.values(response.data[y])
      index[x] = val
      console.log(index)
      axios.put('http://' + hostname + '/api/datadasar/' + index[0], {
        id: index[0],
        name: index[1]
      }).then((res) => {
        console.log(res.data)
      })
    })
}

var addrow = function(instance) {
  axios({
    method: 'post',
    url: 'http://' + hostname + '/api/datadasar/',
    data: {
    }
  })
    .then((response) => {
      console.log(response.data)
    })
}

var delrow = function(instance, id) {
  axios({
    method: 'get',
    url: 'http://' + hostname + '/api/datadasar/'
  })
    .then((response) => {
      var temp = Object.keys(response.data[id]).map(function (key) {
        return response.data[id][key]
      })
      axios
        .delete('http://' + hostname + '/api/datadasar/' + temp[0])
      console.log(response.data)
    })
}

export default {
  name: 'App',
  mounted: function () {
    axios.get('http://' + hostname + '/api/datadasar/').then(res => {
      console.log(res.data)
      var jexcelOptions = {
        data: res.data,
        onchange: update,
        oninsertrow: addrow,
        ondeleterow: delrow,
        allowToolbar: true,
        columns: [
          { type: 'hidden' },
          { type: 'text', title: 'Nama', width: '200px' },
          {type: 'text', title: 'Tgl Dibuat', width: '200px', readOnly: true},
          {type: 'text', title: 'Tgl Dirubah', width: '200px', readOnly: true},
          {type: 'text', title: 'Tgl Kadaluarsa', width: '200px', readOnly: true}
        ]
      }
      let spreadsheet = jexcel(this.$el, jexcelOptions)
      Object.assign(this, { spreadsheet })
    })
  }
}
</script>
