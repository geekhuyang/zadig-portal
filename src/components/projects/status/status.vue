<template>
  <div class="status-detail-wrapper">
    <section v-if="runningCount === 0 && pendingCount === 0"
             class="no-running">
      <img src="@assets/icons/illustration/run_status.svg"
           alt="">
      <p>暂无正在运行的任务</p>
    </section>
    <section v-else
             class="running-time">
      <ProductWorkflowStatus :productWorkflowTasks="productWorkflowTasks"
                     :expandId="productExpandId"></ProductWorkflowStatus>
      <CommonWorkflowStatus :commonWorkflowTasks="commonWorkflowTasks"></CommonWorkflowStatus>
      <TestStatus :testTasks="testTasks"></TestStatus>
    </section>
  </div>
</template>

<script>
import { taskRunningSSEAPI, taskPendingSSEAPI } from '@api'
import bus from '@utils/event_bus'
import ProductWorkflowStatus from './constainer/product_workflow_status'
import CommonWorkflowStatus from './constainer/common_workflow_status'
import TestStatus from './constainer/test_status'
export default {
  data () {
    return {
      task: {
        running: null,
        pending: null
      },
      productWorkflowTasks: {
        pending: [],
        running: []
      },
      commonWorkflowTasks: {
        running: [],
        pending: []
      },
      testTasks: {
        pending: [],
        running: []
      },
      taskDetailExpand: {},
      productExpandId: 0
    }
  },
  methods: {
    showTaskList (type) {
      if (type === 'running') {
        taskRunningSSEAPI()
          .then(res => {
            this.productWorkflowTasks.running = res.data.filter(task => task.type === 'workflow')
            this.testTasks.running = res.data.filter(task => task.type === 'test')
            this.commonWorkflowTasks.running = res.data.filter(task => task.type === 'workflow_v3')
            this.task.running = res.data.length
            if (this.productWorkflowTasks.running.length > 0) {
              this.productExpandId = this.productWorkflowTasks.running[0].task_id
            }
          })
          .closeWhenDestroy(this)
      } else if (type === 'queue') {
        taskPendingSSEAPI()
          .then(res => {
            this.productWorkflowTasks.pending = res.data.filter(task => task.type === 'workflow')
            this.testTasks.pending = res.data.filter(task => task.type === 'test')
            this.commonWorkflowTasks.pending = res.data.filter(task => task.type === 'workflow_v3')
            this.task.pending = res.data.length
          })
          .closeWhenDestroy(this)
      }
    }
  },
  computed: {
    runningCount () {
      return this.task.running
    },
    pendingCount () {
      return this.task.pending
    }
  },
  mounted () {
    this.showTaskList('running')
    this.showTaskList('queue')
    bus.$emit('show-sidebar', true)
    bus.$emit('set-topbar-title', { title: '运行状态', breadcrumb: [] })
    bus.$emit('set-sub-sidebar-title', {
      title: '',
      routerList: []
    })
  },
  components: {
    ProductWorkflowStatus, TestStatus, CommonWorkflowStatus
  }
}
</script>

<style lang="less">
@keyframes blink {
  50% {
    border-left: 5px solid transparent;
  }
}

@keyframes blink-dot {
  50% {
    background-color: transparent;
  }
}

.status-detail-wrapper {
  position: relative;
  flex: 1;
  padding: 1.7em 1rem 5em;
  overflow: auto;

  .divider {
    width: 100%;
    height: 1px;
    background-color: #e6e9f0;
  }

  .task-container {
    margin-bottom: 10px;
  }

  .no-running {
    display: flex;
    flex-direction: column;
    align-content: center;
    align-items: center;

    img {
      width: 480px;
      height: 480px;
    }

    p {
      color: #606266;
      font-size: 15px;
    }
  }
}
</style>
