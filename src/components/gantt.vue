<template>
  <div id="gantt">
    <div class="gantt-wrapper clearfix">
      <div class="gantt-left" ref="ganttLeft">
        <div class="table-responsive">
          <table style="width: 100%;" class="content">
            <colgroup  v-for="item in gantt.titles" :key="item.width">
              <col :width="item.width"/>
            </colgroup>
            <thead>
              <tr>
                <th :colspan="gantt.titles.length" :style="{height: emptyHeight}">
                </th>
              </tr>
              <tr>
                <th v-for="(item, index) in gantt.titles" :key="index">{{item.title}}</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(item, index) in gantt.data" :key="index" v-show="toggleArr.indexOf(index) === -1">
                <td>
                  <i class="state" :class="statusColor[item.status]"></i>
                  <i v-if="item.hasDownload" class="has-download" @click="downloadHandle(item)">DS</i>
                </td>
                <td :title="item.no + '.' + item.name" class="name">
                  <i v-for="n in item.level" :key="n" class="black"></i>
                  <span>
                    <i v-if="item.hasChild" class="toggle" @click="toggleHandle($event, item)">
                      {{!expand ? toggleRight : toggleDown}}
                    </i>
                    {{item.no + '.' + item.name}}
                  </span>
                </td>
                <td class="center">{{item.startDate}}</td>
                <td class="center">{{item.progress}}</td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
      <div class="gantt-right" ref="ganttRight">
        <div class="picker clearfix">
          <div class="pull-left">
            <el-date-picker
              v-model="time"
              type="daterange"
              :picker-options="pickerOptions"
              start-placeholder="开始日期"
              end-placeholder="结束日期">
            </el-date-picker>
          </div>
          <div class="pull-left">
            <div class="select-date">
              <a
                href="javascript:void(0);"
                v-for="item in types"
                :key="item.id"
                @click="dateHandle(item)"
                :class="{current: item.engName === currentDateType}"
              >
                {{item.cnName}}
              </a>
            </div>
          </div>
        </div>
        <div class="table-responsive">
          <table class="table" ref="table">
            <template>
              <thead v-if="currentDateType === 'days'">
                <tr>
                  <th class="fullYear" v-for="(item, index) in rangeDate.months" :key="index" :colspan="colspan(item)">
                    {{monthFilter(item, colspan(item))}}
                  </th>
                </tr>
                <tr>
                  <th v-for="(item, index) in rangeDate.dates" :key="index">
                    <div class="row">
                      {{item|day}}
                    </div>
                  </th>
                </tr>
                <tr>
                  <th v-for="(item, index) in rangeDate.dates" :key="index">
                    <div class="row">
                      {{item|week}}
                    </div>
                  </th>
                </tr>
              </thead>
              <thead v-else-if="currentDateType === 'weeks'">
                <tr>
                  <th v-for="(item, index) in rangeDate.months" :key="index" :colspan="colspan(item)">
                    <div :class="{'row-weeks': colspan(item) > 3}">{{monthFilter(item, colspan(item))}}</div>
                  </th>
                </tr>
                <tr>
                  <th v-for="(item, index) in monthsColspanAllCount" :key="index" :colspan="1">
                    <div class="row">{{rangeDate.weeks[index]}}</div>
                  </th>
                </tr>
              </thead>
              <thead v-else-if="currentDateType === 'months'">
                <tr>
                  <th v-for="(item, index) in rangeDate.months" :key="index" :colspan="colspan(item)">
                    <div :class="{'row-month': colspan(item) > 3}">{{monthFilter(item, colspan(item))}}</div>
                  </th>
                </tr>
              </thead>
            </template>
            <template>
              <tbody v-if="currentDateType === 'days'">
                <tr v-for="(item, index) in gantt.data" :key="index" v-show="toggleArr.indexOf(index) === -1">
                  <td v-for="(item, index) in rangeDate.dates" :key="index"></td>
                </tr>
              </tbody>
              <tbody v-else-if="currentDateType === 'months' || currentDateType === 'weeks'">
                <tr v-for="(item, index) in gantt.data" :key="index" v-show="toggleArr.indexOf(index) === -1">
                  <td v-for="item in monthsColspanAllCount" :key="item"></td>
                </tr>
              </tbody>
            </template>
          </table>
          <div ref="progresses" class="progresses" :style="{top: progressesHeight}">
            <div class="progress-item" v-for="(val, index) in gantt.data" :key="index" v-show="toggleArr.indexOf(index) === -1">
              <div class="pregress" :key="index1" v-for="(item, index1) in val.values" :class="item.customClass" :style="getStyle(item)" :title="item.desc"></div>
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
var DAY = 60 * 60 * 1000 * 24
export default {
  name: 'gantt',
  props: {
    ganttData: {
      type: Array,
      default () {
        return []
      }
    },
    expand: {
      type: Boolean,
      default: false
    }
  },
  data () {
    return {
      monthsColspanAllCount: 0,
      currentDateType: 'months',
      types: [
        {
          id: 1,
          cnName: '日',
          engName: 'days'
        },
        {
          id: 2,
          cnName: '周',
          engName: 'weeks'
        },
        {
          id: 3,
          cnName: '月',
          engName: 'months'
        }
      ],
      time: '',
      // 已完成 正常进行中 未开始 进行中（延期1天以上10天以下） 进行中（延期10天以上）
      statusColor: ['s-blue', 's-green', 's-white', 's-yellow', 's-red'],
      shortcuts: [
        {
          text: '',
          onClick: () => {
            this.time3 = [ new Date(), new Date() ]
          }
        }
      ],
      ceilHeight: 27,
      ceilWidth: 27,
      currentMonth: '',
      gantt: {
        titles: [
          {
            title: '状态',
            width: '20px'
          },
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
        data: []
      },
      rangeDate: {
        dates: [],
        months: [],
        weeks: []
      },
      daysBefore: 0,
      daysAfter: 0,
      toggleDown: '▼',
      toggleRight: '▶',
      toggleArr: [],
      startTime: Date.now(),
      endTime: Date.now(),
      months: [],
      one: 1
    }
  },
  computed: {
    data2 () {
      return this.ganttData
    },
    pickerOptions () {
      var self = this
      return {
        disabledDate (time) {
          return time.getTime() < new Date(self.startTime).getTime() - DAY
        }
      }
    },
    emptyHeight () {
      if (this.currentDateType === 'days') {
        return '100px'
      } else if (this.currentDateType === 'weeks') {
        return '75px'
      } else if (this.currentDateType === 'months') {
        return '48px'
      }
      return '100px'
    },
    progressesHeight () {
      if (this.currentDateType === 'days') {
        return '78px'
      } else if (this.currentDateType === 'weeks') {
        return '52px'
      } else if (this.currentDateType === 'months') {
        return '26px'
      }
      return '78px'
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
    }
  },
  watch: {
    data2: {
      handler (newVal) {
        this.$set(this.gantt, 'data', newVal)
      },
      immediate: true
    },
    time (newVal) {
      this.$set(this.gantt, 'data', JSON.parse(JSON.stringify(this.data2)))
    },
    'gantt.data': {
      handler (newVal) {
        if (newVal && newVal.length === 0) {
          this.data2 = []
          this.toggleArr = []
          this.time = ''
          this.one = 1
          return
        }
        var max, min
        for (var i = 0, len = newVal.length; i < len; i++) {
          var val = newVal[i].values
          for (var j = 0, len1 = val.length; j < len1; j++) {
            val[j].from = this.initTime(+val[j].from).getTime()
            val[j].to = this.initTime(+val[j].to).getTime() + 10
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
              if (from > 0) {
                min = from
              }
            } else {
              if (min > from && from > 0) {
                min = from
              }
            }
          }
        }
        min = +min
        max = +max
        this.startTime = this.initTime(min)
        this.endTime = this.initTime(max)
        this.rangeDate.start = this.initTime(min)
        this.rangeDate.end = this.initTime(max)
        if ((this.time && this.time.length > 0) || (min > 0 && max > 0)) {
          if (this.time && this.time.length > 0) {
            min = new Date(this.time[0]).getTime()
            max = new Date(this.time[1]).getTime()
            this.rangeDate.start = this.initTime(min)
            this.rangeDate.end = this.initTime(max)
          } else if (min > 0 && max > 0) {
            this.rangeDate.start = this.startTime
            this.rangeDate.end = this.endTime
          }

          this.rangeDate.dates.length = 0
          this.rangeDate.months.length = 0
          this.rangeDate.weeks.length = 0
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
            var w = Math.floor((d.getTime() - this.startTime.getTime()) / (DAY * 7)) + 1
            if (this.rangeDate.weeks.length === 0) {
              this.rangeDate.weeks.push(w)
            } else {
              if (this.rangeDate.weeks.indexOf(w) === -1) {
                this.rangeDate.weeks.push(w)
              }
            }
            this.rangeDate.dates.push(d.getFullYear() + '-' + (d.getMonth() + 1) + '-' + d.getDate() + ':' + d.getDay())
            min += DAY
          } while (min <= max)
          var self = this
          setTimeout(function () {
            self.getTableWidth()
          }, 5)
          this.monthsColspanAllCount = this.getMonthsColspanAll()
          if (!this.expand && this.one++ === 1) {
            this.gantt.data.forEach(function (item, index) {
              if (item.no.indexOf('-') > -1) {
                self.toggleArr.push(index)
              }
            })
            document.querySelectorAll('.toggle').innerText = this.toggleRight
          }
          this.months = this.rangeDate.months
        }
      },
      immediate: true
    }
  },
  methods: {
    reset () {
      this.$set(this.gantt, 'data', JSON.parse(JSON.stringify([])))
    },
    getMonthsColspanAll () {
      var start = this.rangeDate.start
      var end = this.rangeDate.end
      var len = 0
      var timeStart = start.getDate()
      var timeEnd = end.getDate()
      var months = this.rangeDate.months
      var time = timeEnd - timeStart > 0 ? timeEnd - timeStart : 1
      len = months.length === 1 ? Math.ceil((time) / 7) : this.rangeDate.months.length * 4 - this.getMonthsColspan(start.getDate(), 'after') - this.getMonthsColspan(end.getDate(), 'before') + 2

      return len
    },
    monthFilter (val, colspan) {
      var startIndex = val.indexOf('-')
      var year = val.slice(0, startIndex)
      var monthIndex = val.slice(startIndex + 1).match(/\d+/)[0]
      if (colspan <= 2) {
        return month[monthIndex - 1]
      }
      return year + '年' + month[monthIndex - 1]
    },
    getTableWidth () {
      var self = this
      this.$nextTick(function () {
        self.$refs.progresses.style.width = self.$refs.table.offsetWidth + 'px'
        self.$refs.progresses.style.overflow = 'hidden'
      })
    },
    initFullYear () {
      setTimeout(function () {
        var fullYear = document.querySelectorAll('.fullYear')
        fullYear.forEach(function (item) {
          var num = +item.getAttribute('colspan')
          if (num <= 2) {
            var text = item.innerText
            var index = text.indexOf('年')
            item.innerText = text.slice(index + 1)
          }
        })
      }, 5)
    },
    initTime (date) {
      var d = new Date(date)
      d.setHours(0)
      d.setMinutes(0)
      d.setSeconds(1)
      return d
    },
    toggleHandle (e, item) {
      var no = item.no
      var arr = []
      var data = this.gantt.data
      for (var i = 0, len = data.length; i < len; i++) {
        if (data[i].no !== no && data[i].no.indexOf(no) === 0) {
          arr.push(i)
        }
      }

      if (e.target.innerText === this.toggleDown) {
        e.target.innerText = this.toggleRight
        for (var k = 0, len2 = arr.length; k < len2; k++) {
          if (this.toggleArr.indexOf(arr[k]) === -1) {
            this.toggleArr.push(arr[k])
          }
        }
      } else {
        e.target.innerText = this.toggleDown
        var reg = new RegExp('^' + no + '-\\d$')
        for (var j = 0, len1 = arr.length; j < len1; j++) {
          if (reg.test(data[arr[j]].no)) {
            var index = this.toggleArr.indexOf(arr[j])
            this.toggleArr.splice(index, 1)
          }
        }

        var toggleIcon = []
        for (var h = 0, len3 = data.length; h < len3; h++) {
          if (data[h].no !== no && data[h].no.indexOf(no) > -1 && data[h].hasChild) {
            toggleIcon.push(h)
          }
        }
        var self = this
        toggleIcon.forEach(function (item) {
          var tr = document.querySelectorAll('.content tbody tr')[item]
          tr.querySelector('.toggle').innerText = self.toggleRight
        })
      }
    },
    colspan (val) {
      var l = 0
      if (this.currentDateType === 'days') {
        for (var i = 0, len = this.rangeDate.dates.length; i < len; i++) {
          var reg = new RegExp(val)
          if (reg.test(this.rangeDate.dates[i])) {
            l++
          }
        }
      } else if (this.currentDateType === 'months' || this.currentDateType === 'weeks') {
        var timeStart = this.rangeDate.start.getDate()
        var timeEnd = this.rangeDate.end.getDate()
        var months = this.rangeDate.months
        if (months.length === 1) {
          l = Math.ceil((timeEnd - timeStart) / 7)
        } else if (months.indexOf(val) === 0 && months.length > 1) {
          l = this.getMonthsColspan(timeStart, 'before')
        } else if (months.indexOf(val) === months.length - 1) {
          l = this.getMonthsColspan(timeEnd, 'after')
        } else {
          l = 4
        }
      }
      return l
    },
    getMonthsColspan (day, type) {
      type = type || 'before'
      if (type === 'before') {
        if (day <= 7) {
          return 4
        } else if (day > 7 && day <= 15) {
          return 3
        } else if (day > 15 && day <= 21) {
          return 2
        } else {
          return 1
        }
      } else {
        if (day <= 7) {
          return 1
        } else if (day > 7 && day <= 15) {
          return 2
        } else if (day > 15 && day <= 21) {
          return 3
        } else {
          return 4
        }
      }
    },
    getStyle (item) {
      var from = +item.from
      var to = +item.to
      if (!to || !from || to === 0 || from === 0 || to <= 0 || from <= 0) {
        return {width: 0}
      }
      var min = this.rangeDate.start
      if (this.currentDateType === 'days') {
        return this.dayProgress(from, to, min)
      } else if (this.currentDateType === 'weeks') {
        return this.weekProgress(from, to)
      } else if (this.currentDateType === 'months') {
        return this.monthProgress(from, to)
      }
      return {width: 0}
    },
    dayProgress (from, to, min) {
      if (min > 0) {
        var marginLeft = Math.floor((from - min) / DAY) * this.ceilWidth
        var result = 0
        if (to - from > 0) {
          result = to - from
        } else if (to - from === 0) {
          result = 1
        }
        var width = Math.ceil(result / DAY) * this.ceilWidth
        return {width: width + 'px', marginLeft: marginLeft + 'px'}
      }
      return {}
    },
    weekProgress (from, to) {
      return this.monthProgress(from, to)
    },
    monthProgress (from, to) {
      var startTime = this.startTime
      var ys = startTime.getFullYear()
      var ms = startTime.getMonth() + 1
      var timeS = ys + '-' + ms
      var marginLeft = 0
      var width = 0
      var date = new Date(from)
      var y = date.getFullYear()
      var m = date.getMonth() + 1
      var d = date.getDate()
      var time = y + '-' + m
      var date1 = new Date(to)
      var y1 = date1.getFullYear()
      var m1 = date1.getMonth() + 1
      var d1 = date1.getDate()
      var index = 0

      var minDate = startTime.getDate()
      var minDateIndex = 0
      if (minDate <= 7) {
        minDateIndex = 0
      } else if (minDate > 7 && minDate <= 15) {
        minDateIndex = 1
      } else if (minDate > 15 && minDate <= 21) {
        minDateIndex = 2
      } else {
        minDateIndex = 3
      }

      index = this.months.indexOf(time)
      if (this.time) {
        var currentStartTime = this.time[0]
        var currentY = currentStartTime.getFullYear()
        var currentM = currentStartTime.getMonth() + 1
        var currentD = currentStartTime.getDate()
        var curTime = currentY + '-' + currentM
        var a = timeS.split('-')
        var b = curTime.split('-')
        var c = a[0] - b[0]
        var e = a[1] - b[1]

        marginLeft -= (c * 12 + e + 1) * this.ceilWidth

        if (currentD <= 7) {
          marginLeft -= this.ceilWidth * 3
        } else if (currentD > 7 && currentD <= 15) {
          marginLeft -= this.ceilWidth * 2
        } else if (currentD > 15 && currentD <= 21) {
          marginLeft -= this.ceilWidth * 1
        } else {
          marginLeft -= this.ceilWidth * 0
        }
      }
      if (d <= 7) {
        marginLeft += (index * this.ceilWidth * 4)
        if (this.time) {
          marginLeft -= this.ceilWidth * 4
        }
      } else if (d > 7 && d <= 15) {
        marginLeft += (index * this.ceilWidth * 4) + this.ceilWidth
        if (this.time) {
          marginLeft -= this.ceilWidth * 3
        }
      } else if (d > 15 && d <= 21) {
        marginLeft += (index * this.ceilWidth * 4) + this.ceilWidth * 2
        if (this.time) {
          marginLeft -= this.ceilWidth * 2
        }
      } else {
        marginLeft += (index * this.ceilWidth * 4) + this.ceilWidth * 3
        if (this.time) {
          marginLeft -= this.ceilWidth * 1
        }
      }
      marginLeft -= this.ceilWidth * minDateIndex

      if (y1 === y) {
        if (m1 === m) {
          width += this.getDayWidth(d1) - this.getDayWidth(d) + this.ceilWidth
        } else if (m1 > m) {
          width += (m1 - m) * this.ceilWidth * 4 + this.getDayWidth(d1) - this.getDayWidth(d) + this.ceilWidth
        }
      } else if (y1 > y) {
        width += (y1 - y) * 4 * 12 * this.ceilWidth
        if (m1 === m) {
          width += this.getDayWidth(d1) - this.getDayWidth(d) + this.ceilWidth
        } else if (m1 > m) {
          width += (m1 - m) * this.ceilWidth * 4 + this.getDayWidth(d1) - this.getDayWidth(d) + this.ceilWidth
        } else {
          width += -(m - m1) * this.ceilWidth * 4 + this.getDayWidth(d1) - this.getDayWidth(d) + this.ceilWidth
        }
      }
      marginLeft = marginLeft > 0 ? marginLeft - 1 : marginLeft + 1
      return {width: (width - 1) + 'px', marginLeft: marginLeft + 'px'}
    },
    getDayWidth (d) {
      if (d <= 7) {
        return this.ceilWidth
      } else if (d > 7 && d <= 15) {
        return this.ceilWidth * 2
      } else if (d > 15 && d <= 21) {
        return this.ceilWidth * 3
      } else {
        return this.ceilWidth * 4
      }
    },
    dateHandle (item) {
      this.currentDateType = item.engName
      this.initFullYear()
      var self = this
      setTimeout(function () {
        self.getTableWidth()
      }, 5)
    },
    downloadHandle (item) {
      this.$emit('see', item.slice(0, 1))
    }
  }
}
</script>

<style>
</style>
