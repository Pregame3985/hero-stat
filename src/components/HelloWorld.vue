<template>
  <chart :options="line" auto-resize style="position: absolute; left: 0; top: 100px; width: 1400px; height: 800px;"/>
</template>

<script>
import ECharts from 'vue-echarts/components/ECharts'
import 'echarts/lib/chart/line'
import 'echarts/lib/component/tooltip'
import 'echarts/lib/component/legend'
import 'echarts/lib/component/title'
import request from 'superagent'

export default {
  components: {
    chart: ECharts
  },
  data: function () {
    const xAxises = []

    const ids = [
      '0xd96a094a0000000000000000000000000000000000000000000000000000000000000001',
      '0xd96a094a0000000000000000000000000000000000000000000000000000000000000002',
      '0xd96a094a0000000000000000000000000000000000000000000000000000000000000003',
      '0xd96a094a0000000000000000000000000000000000000000000000000000000000000004',
      '0xd96a094a0000000000000000000000000000000000000000000000000000000000000005',
      '0xd96a094a0000000000000000000000000000000000000000000000000000000000000006',
      '0xd96a094a0000000000000000000000000000000000000000000000000000000000000007',
      '0xd96a094a0000000000000000000000000000000000000000000000000000000000000008',
      '0xd96a094a0000000000000000000000000000000000000000000000000000000000000009',
      '0xd96a094a0000000000000000000000000000000000000000000000000000000000000010',
      '0xd96a094a00000000000000000000000000000000000000000000000000000000000000a',
      '0xd96a094a00000000000000000000000000000000000000000000000000000000000000b',
      '0xd96a094a00000000000000000000000000000000000000000000000000000000000000c',
      '0xd96a094a00000000000000000000000000000000000000000000000000000000000000d',
      '0xd96a094a00000000000000000000000000000000000000000000000000000000000000e',
      '0xd96a094a00000000000000000000000000000000000000000000000000000000000000f',
      '0xd96a094a000000000000000000000000000000000000000000000000000000000000006d'
    ]
    const names = [
      '宋江',
      '卢俊义',
      '无用',
      '公孙胜',
      '关胜',
      '林冲',
      '秦明',
      '呼延灼',
      '花荣',
      '柴进',
      '李应',
      '朱仝',
      '鲁智深',
      '武松',
      '董平',
      '张清',
      '蔡京'
    ]
    const series = []

    for (let i = 0; i < names.length; i++) {
      xAxises.push({
        type: 'category',
        axisTick: {
          alignWithLabel: true
        },
        axisLine: {
          onZero: false,
          lineStyle: {
            color: '#' + this.intToRGB(this.hashCode(names[i]))
          }
        },
        axisPointer: {
          label: {
            formatter: function (params) {
              return names[i]
            }
          }
        },
        data: []
      })

      series.push({
        name: names[i],
        smooth: true,
        symbolSize: 10,
        type: 'line',
        data: []
      })
    }
    request.get('https://api.etherscan.io/api?module=account&action=txlist&address=0xd0792aC0de7Ef31197C5f452B21A34389eCc725f&startblock=5107373&endblock=99999999&sort=asc&apikey=29F67QW7J7XD6I6M3IVMJ3Z94J9XGIZGQB')
      .set('Accept', 'application/json')
      .then((data) => {
        if (data.body.message === 'OK') {
          data.body.result.forEach(e => {
            if (e.isError === '0') {
              // let a = new Date()
              // a.setTime(e.timeStamp * 1000)
              // let hour = a.getHours()
              // let min = a.getMinutes()
              // let sec = a.getUTCSeconds()
              // let time = hour + ':' + min + ':' + sec
              // timeData.push(time)

              for (let i = 0; i < ids.length; i++) {
                if (e.input === ids[i]) {
                  xAxises[i].data.push('')
                  series[i].data.push((e.value) / 1000000000000000000)
                }
              }
            }
          })
        }
      })

    return {
      line: {
        tooltip: {
          trigger: 'none',
          axisPointer: {
            type: 'cross'
          }
        },
        legend: {
          data: names
        },
        xAxis: xAxises,
        yAxis: {
          type: 'value'
        },
        series: series
      }
    }
  },
  methods: {

    hashCode (str) { // java String#hashCode
      let hash = 0
      for (let i = 0; i < str.length; i++) {
        hash = str.charCodeAt(i) + ((hash << 5) - hash)
      }
      return hash
    },

    intToRGB (i) {
      let c = (i & 0x00FFFFFF)
        .toString(16)
        .toUpperCase()

      return '00000'.substring(0, 6 - c.length) + c
    }
  }
}
</script>
