<template>
  <div>
    <el-dialog
      title="提示"
      :visible.sync="dialogVisible"
      :fullscreen="true"
      :before-close="handleClose">
      <gantt
        :ganttData="ganttData"
        :expand="true"
      />
    </el-dialog>
    <el-button type="text" @click="open">点击打开 Dialog</el-button>
  </div>
</template>

<script>
import gantt from './gantt'
export default {
  name: 'HelloWorld',
  data () {
    return {
      dialogVisible: false,
      ganttData: [],
      data: [
        {
          no: '1',
          name: '立项阶段',
          startDate: '2018-06-23',
          progress: '50%',
          level: 0,
          hasChild: true,
          status: 1,
          values: [
            {
              from: '1530306400000',
              to: '1531706400000',
              desc: '2018-06-30 —— 2018-07-16',
              label: '',
              customClass: 'ganttBlue'
            },
            {
              from: '1530206400000',
              to: '1533106400000',
              desc: '2018-06-29 —— 2018-08-1',
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
          level: 1,
          hasChild: true,
          status: 0,
          hasDownload: true,
          values: [
            {
              from: '1530806400000',
              to: '1564606400000',
              desc: '2018-07-06 —— 2019-08-01',
              label: '',
              customClass: 'ganttOrange'
            }
          ]
        },
        {
          no: '1-1-1',
          name: '项目申报书',
          startDate: '2018-07-06',
          progress: '50%',
          level: 2,
          hasChild: false,
          status: 0,
          values: [
            {
              from: '1533806400000',
              to: '1540906400000',
              desc: '2018-08-09 —— 2018-10-30',
              label: '',
              customClass: 'ganttOrange'
            }
          ]
        },
        {
          no: '1-2',
          name: '项目申报书1',
          startDate: '2018-07-06',
          progress: '50%',
          level: 1,
          hasChild: false,
          status: 0,
          values: [
            {
              from: '1530806400000',
              to: '1560906400000',
              desc: '2018-07-06 —— 2019-06-19',
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
          level: 0,
          hasChild: true,
          status: 0,
          values: [
            {
              from: '1530374400000',
              to: '1530374400000',
              desc: '2018-07-01 —— 2018-07-01',
              label: '',
              customClass: 'ganttBlue'
            }
          ]
        },
        {
          no: '2-1',
          name: '采购阶段',
          startDate: '2018-07-01 ',
          progress: '50%',
          level: 1,
          hasChild: false,
          status: 0,
          values: [
            {
              from: '',
              to: '',
              desc: '2018-07-01 —— 2018-07-01',
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
          level: 0,
          hasChild: false,
          status: 0,
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
          level: 0,
          hasChild: false,
          status: 0,
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
          level: 0,
          hasChild: false,
          status: 0,
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
    }
  },
  components: {gantt},
  methods: {
    open () {
      this.ganttData = JSON.parse(JSON.stringify(this.data))
      this.dialogVisible = true
    },
    handleClose () {
      this.dialogVisible = false
      this.ganttData = []
    }
  }
}
</script>
