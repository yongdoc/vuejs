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
    .get('http://' + hostname + '/api/indikatorperiode/')
    .then((response) => {
      var index = Object.values(response.data[y])
      index[x] = val
      console.log(index)
      axios.put('http://' + hostname + '/api/indikatorperiode/' + index[0], {
        id: index[0],
        id_master: index[1],
        id_periode: index[2],
        bobot: index[3]
      }).then((res) => {
        console.log(res.data)
      })
    })
}

var addrow = function(instance) {
  axios({
    method: 'post',
    url: 'http://' + hostname + '/api/indikatorperiode/',
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
    url: 'http://' + hostname + '/api/indikatorperiode/'
  })
    .then((response) => {
      var temp = Object.keys(response.data[id]).map(function (key) {
        return response.data[id][key]
      })
      axios
        .delete('http://' + hostname + '/api/indikatorperiode/' + temp[0])
      console.log(response.data)
    })
}

export default {
  name: 'App',
  mounted: function () {
    axios.get('http://' + hostname + '/api/indikatorperiode/').then(res => {
      console.log(res.data)
      var jexcelOptions = {
        data: res.data,
        onchange: update,
        oninsertrow: addrow,
        ondeleterow: delrow,
        allowToolbar: true,
        pagination: 15,
        wordWrap: true,
        tableOverflow: true,
        tableHeight: '700px',
        columns: [
          { type: 'hidden' },
          { type: 'text', title: 'Master Indikator', width: '200px' },
          { type: 'text', title: 'Periode', width: '200px' },
          { type: 'float', title: 'Bobot', width: '100px' }
        ]
      }
      let spreadsheet = jexcel(this.$el, jexcelOptions)
      Object.assign(this, { spreadsheet })
    })
  }
}
</script>
