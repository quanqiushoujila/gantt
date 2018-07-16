<template>
  <div id="gantt">
    <div class="gantt-wrapper clearfix">
      <div class="gantt-left" ref="ganttLeft">
        <div class="table-responsive">
          <table style="width: 100%;" class="">
            <colgroup  v-for="item in gantt.titles" :key="item.width">
              <col :width="item.width"/>
            </colgroup>
            <thead>
              <tr>
                <th :colspan="gantt.titles.length">
                </th>
              </tr>
              <tr>
                <th v-for="item in gantt.titles" :key="item.titel">{{item.title}}</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="item in gantt.data" :key="item.no">
                <td :title="item.no + '.' + item.name">{{item.no + '.' + item.name}}</td>
                <td :title="item.startDate">{{item.startDate}}</td>
                <td :title="item.progress">{{item.progress}}</td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
      <div class="gantt-right" ref="ganttRight">
        <div class="picker">
          <input type="date">
        </div>
        <div class="table-responsive">
          <table>
            <thead>
              <tr>
                <th v-for="item in rangeDate.months" :colspan="colspan(item)">
                  {{item|month}}
                </th>
              </tr>
              <tr>
                <th v-for="item in rangeDate.dates">{{item|day}}</th>
              </tr>
              <tr>
                <th v-for="(item, index) in rangeDate.dates">{{item|week}}</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="item in gantt.data" :key="item.name">
                <th v-for="(item, index) in rangeDate.dates"></th>
              </tr>
            </tbody>
          </table>
          <div class="progresses">
            <div class="progress-item" v-for="val in this.gantt.data">
              <div class="pregress" v-for="item in val.values" :class="item.customClass" :style="getStyle(item)"></div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
var week = ['日', '一', '二', '三', '四', '五', '六']
var month = ['一月', '二月', '三月', '四月', '五月', '六月', '七月', '八月', '九月', '十月', '十一月', '十二月']
export default {
  name: 'HelloWorld',
  data () {
    return {
      ceilHeight: 26,
      ceilWidth: 23,
      currentMonth: '',
      gantt: {
        titles: [
          {
            title: '任务名称',
            width: '200px'
          },
          {
            title: '计划开始时间',
            width: '100px'
          },
          {
            title: '进度',
            width: '50px'
          }
        ],
        data: [
          {
            no: '1',
            name: '立项阶段',
            startDate: '2018-06-23',
            progress: '50%',
            values: [
              {
                from: '1530806400000',
                to: '1531706400000',
                desc: '2018-07-06 —— 2018-07-06',
                label: '',
                customClass: 'ganttBlue'
              },
              {
                from: '1530806400000',
                to: '1531806400000',
                desc: '2018-07-06 —— 2018-07-06',
                label: '',
                customClass: 'ganttOrange'
              }
            ]
          },
          {
            no: '1-1',
            name: '项目申报书',
            startDate: '2018-07-06',
            progress: '50%',
            values: [
              {
                from: '1530806400000',
                to: '1530806400000',
                desc: '2018-07-06',
                label: '',
                customClass: 'ganttOrange'
              }
            ]
          },
          {
            no: '2',
            name: '采购阶段',
            startDate: '2018-07-01 ',
            progress: '50%',
            values: [
              {
                from: '1530374400000',
                to: '1531252000000',
                desc: '2018-07-01 —— 2018-07-10',
                label: '',
                customClass: 'ganttBlue'
              }
            ]
          },
          {
            no: '3',
            name: '实施阶段',
            startDate: '2018-07-01 ',
            progress: '50%',
            values: [
              {
                from: '1530374400000',
                to: '1531584000000',
                desc: '2018-07-01 —— 2018-07-15',
                label: '',
                customClass: 'ganttOrange'
              }
            ]
          },
          {
            no: '4',
            name: '验收与支付',
            startDate: '2018-07-01 ',
            progress: '50%',
            values: [
              {
                from: '1530374400000',
                to: '1532448000000',
                desc: '2018-07-01 —— 2018-07-25',
                label: '',
                customClass: 'ganttOrange'
              }
            ]
          },
          {
            no: '5',
            name: '项目绩效',
            startDate: '2018-07-01 ',
            progress: '50%',
            values: [
              {
                from: '1531152000000',
                to: '1532016000000',
                desc: '2018-07-10 —— 2018-07-20',
                label: '',
                customClass: 'ganttOrange'
              }
            ]
          }
        ]
      },
      addBefore: 3,
      addAfter: 3,
      rangeDate: {
        dates: [],
        months: []
      }
    }
  },
  filters: {
    week (val) {
      var index = val.indexOf(':')
      return week[val.slice(index + 1)]
    },
    day (val) {
      var startIndex = val.lastIndexOf('-')
      var endIndex = val.indexOf(':')
      return val.slice(startIndex + 1, endIndex)
    },
    month (val) {
      var startIndex = val.indexOf('-')
      var year = val.slice(0, startIndex)
      var monthIndex = val.slice(startIndex + 1).match(/\d+/)[0]
      return year + '年' + month[monthIndex - 1]
    }
  },
  watch: {
    gantt: {
      handler (newVal) {
        console.log(newVal)
        var max, min
        for (var i = 0, len = newVal.data.length; i < len; i++) {
          var val = newVal.data[i].values
          for (var j = 0, len1 = val.length; j < len1; j++) {
            var from = val[j].from
            var to = val[j].to
            if (!max) {
              max = to
            } else {
              if (max < to) {
                max = to
              }
            }
            if (!min) {
              min = from
            } else {
              if (min > from) {
                min = from
              }
            }
          }
        }
        min = +min
        max = +max
        this.rangeDate.start = new Date(min)
        this.rangeDate.end = new Date(max)
        var day = 1000 * 60 * 60 * 24
        do {
          var d = new Date(+min)
          var m = d.getFullYear() + '-' + (d.getMonth() + 1)
          if (this.rangeDate.months.length > 0) {
            if (this.rangeDate.months.indexOf(m) === -1) {
              this.rangeDate.months.push(m)
            }
          } else {
            this.rangeDate.months.push(m)
          }
          this.rangeDate.dates.push(d.getFullYear() + '-' + (d.getMonth() + 1) + '-' + d.getDate() + ':' + d.getDay())
          min += day 
        } while(min <= max)
      },
      immediate: true
    }
  },
  methods: {
    word (name) {

    },
    colspan (val) {
      var l = 0
      for (var i = 0, len = this.rangeDate.dates.length; i < len; i++) {
        var reg = new RegExp(val)
        if (reg.test(this.rangeDate.dates[i])) {
          l++
        }
      }
      return l
    },
    getStyle (item) {
      return {width: '100px'}
    }
  }
}
</script>

<style scoped lang="scss">
#gantt {
  padding: 10px;
  .gantt-wrapper {
    border: 1px solid #d7d7d7;
    .gantt-left, .gantt-right {
      min-height: 100px;
      max-height: 300px;
      width: 50%;
      float: left;
      box-sizing: border-box;
      overflow: auto;
      position: relative;
    }
    .gantt-left {
      table {
        tr:first-child {
          th {
            height: 100px;
          }
        }
      }
    }
    .gantt-right {
      .picker {
        height: 48px;
        border-bottom: 1px solid #d7d7d7;
      }
    }
  }
  .ganttBlue {
    background-color: #4b9ccb;
  }
  .ganttOrange {
    background-color: #fdb143;
  }
  .table-responsive {
    position: relative;
    .progresses {
      position: absolute;
      top: 78px;
      left: 0;
      right: 0;
      bottom: 0;
      .progress-item {
        height: 21px;
        position: relative;
      }
      .pregress {
        height: 5px;
        border-radius: 5px;
        margin-top: 5px;
        &.ganttOrange {
          background-color: orange;
        }
        &.ganttBlue {
          background-color: blue;
        }
      }
    }
  }
 
}
.table-responsive>table>tbody>tr>td,
.table-responsive>table>tbody>tr>th,
.table-responsive>table>tfoot>tr>td,
.table-responsive>table>tfoot>tr>th,
.table-responsive>table>thead>tr>td,
.table-responsive>table>thead>tr>th {
  white-space: nowrap;
}
td, th {
  height: 25px;
  padding: 0 3px;
}
th {
  font-weight: 500;
}
table, tr, td, th {
  border-collapse:collapse;
  border: 1px solid #d7d7d7;
}
.clearfix:after {
  content:"";
  display: block;
  clear:both;
}
</style>
