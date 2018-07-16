<template>
  <div id="gantt">
    <div class="gantt-wrapper">
      <div class="gantt-left">
        <table style="width: 100%;">
          <thead>
            <tr>
              <td></td>
            </tr>
            <tr>
              <td v-for="item in gantt.titles" :key="item.titel">{{item.title}}</td>
            </tr>
          </thead>
          <tbody>
            <tr>
              <td></td>
            </tr>
          </tbody>
        </table>
      </div>
      <div class="gantt-right">

      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      gantt: {
        titles: [
          {
            title: '任务名称'
          },
          {
            title: '计划开始时间'
          },
          {
            title: '进度'
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
            no: '1.1',
            name: '项目申报书',
            startDate: '2018-07-06',
            progress: '50%',
            values: [
              {
                from: '/1530806400000/',
                to: '/1530806400000/',
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
                from: '/1530374400000/',
                to: '/1531252000000/',
                desc: '2018-07-01 —— 2018-07-10',
                label: '',
                customClass: 'ganttBlue'
              }
            ]
          },
          {
            no: '3',
            name: '3.实施阶段',
            startDate: '2018-07-01 ',
            progress: '50%',
            values: [
              {
                from: '/1530374400000/',
                to: '/1531584000000/',
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
                from: '/1530374400000',
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
                from: '/1531152000000/',
                to: '/1532016000000/',
                desc: '2018-07-10 —— 2018-07-20',
                label: '',
                customClass: 'ganttOrange'
              }
            ]
          }
        ]
      }
    }
  }
}
</script>

<style scoped lang="scss">
#gantt {
  padding: 10px;
  .gantt-wrapper {
    .gantt-left, .gantt-right {
      min-height: 100px;
      max-height: 300px;
      width: 50%;
      float: left;
      border: 1px solid #d7d7d7;
      box-sizing: border-box;
      overflow: auto;
    }
    .gantt-left {
    }
    .gantt-right {
    }
  }
  .ganttBlue {
    background-color: #4b9ccb;
  }
  .ganttOrange {
    background-color: #fdb143;
  }
}
table {
}
table, tr, td, th {
  border-collapse:collapse;
  border: 1px solid #d7d7d7;
}
</style>
