<template>
  <div class="integration-home">
    <div class="tab-container">
      <el-tabs @tab-click="changeTab" type="card" style="height: 200px;" v-model="currentTab">
        <el-tab-pane name="code" label="代码源集成">
          <keep-alive>
            <Code v-if="currentTab === 'code'" />
          </keep-alive>
        </el-tab-pane>
        <el-tab-pane name="githubapp" label="GitHub App 集成">
          <keep-alive>
            <GitHubApp v-if="currentTab === 'githubapp'" />
          </keep-alive>
        </el-tab-pane>
        <el-tab-pane name="account" label="用户账户集成">
          <keep-alive>
            <Account v-if="currentTab === 'account'" />
          </keep-alive>
        </el-tab-pane>
        <el-tab-pane name="external" label="外部系统集成">
          <keep-alive>
            <External v-if="currentTab === 'external'" />
          </keep-alive>
        </el-tab-pane>
        <el-tab-pane name="mail" label="邮件集成">
          <keep-alive>
            <Mail v-if="currentTab === 'mail'" />
          </keep-alive>
        </el-tab-pane>
      </el-tabs>
    </div>
  </div>
</template>
<script>
import bus from '@utils/event_bus'
import Code from './code.vue'
import GitHubApp from './github_app.vue'
import Account from './account.vue'
import Mail from './mail.vue'
import External from './external.vue'

export default {
  name: 'integration',
  components: {
    Code,
    GitHubApp,
    Account,
    Mail,
    External
  },
  data () {
    return {
      currentTab: 'code'
    }
  },
  methods: {
    showCurrentTab () {
      const currentTab = this.$route.query.currentTab
      if (currentTab) {
        this.currentTab = currentTab
      }
    },
    changeTab (detail) {
      this.$router.replace({
        path: '/v1/system/integration',
        query: { currentTab: detail.name }
      })
    }
  },
  computed: {},
  mounted () {
    bus.$emit('set-topbar-title', { title: '集成管理', breadcrumb: [] })
    bus.$emit('set-sub-sidebar-title', {
      title: '',
      routerList: []
    })
    this.showCurrentTab()
  }
}
</script>

<style lang="less" >
.integration-home {
  position: relative;
  flex: 1;
  margin-top: 15px;
  padding: 15px 30px;
  overflow: auto;

  .sync-container {
    padding-top: 15px;
    padding-bottom: 15px;

    .switch-span {
      display: inline-block;
      height: 20px;
      margin-right: 5px;
      margin-left: 10px;
      font-weight: 500;
      font-size: 14px;
      line-height: 20px;
      vertical-align: middle;
      transition: color 0.5s;
    }
  }
}
</style>
