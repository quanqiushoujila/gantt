<template>
  <div id="gantt">
    <div class="gantt-wrapper">
      <div class="gantt-left">
        <div class="table-responsive">
          <table style="width: 100%;" class="">
            <colgroup  v-for="item in gantt.titles" :key="item.width">
              <col :width="item.width"/>
            </colgroup>
            <thead>
              <tr>
                <th :colspan="gantt.titles.length">
                  111111
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
  },
  methods: {
    word (name) {

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
.table-responsive>table>tbody>tr>td,
.table-responsive>table>tbody>tr>th,
.table-responsive>table>tfoot>tr>td,
.table-responsive>table>tfoot>tr>th,
.table-responsive>table>thead>tr>td,
.table-responsive>table>thead>tr>th {
  white-space: nowrap;
}
table {
}
table, tr, td, th {
  border-collapse:collapse;
  border: 1px solid #d7d7d7;
}
</style>
