<template>
  <div class="config-home">
    <div class="tab-container">
      <el-tabs v-model="activeTab" type="card">
        <el-tab-pane name="quota" label="系统配额">
          <keep-alive >
            <Quota v-if="activeTab === 'quota'" />
          </keep-alive>
        </el-tab-pane>
        <el-tab-pane name="proxy" label="代理配置">
          <keep-alive >
            <Proxy v-if="activeTab === 'proxy'" />
          </keep-alive>
        </el-tab-pane>
        <el-tab-pane name="cache" label="缓存清理">
          <keep-alive >
            <Cache v-if="activeTab === 'cache'" />
          </keep-alive>
        </el-tab-pane>
        <el-tab-pane name="link" label="快捷链接">
          <keep-alive >
            <Link v-if="activeTab === 'link'" />
          </keep-alive>
        </el-tab-pane>
      </el-tabs>
    </div>
  </div>
</template>
<script>
import Quota from './quota.vue'
import Proxy from './proxy.vue'
import Cache from './cache.vue'
import Link from './link.vue'
import bus from '@utils/event_bus'

export default {
  name: 'config',
  components: {
    Proxy,
    Cache,
    Quota,
    Link
  },
  data () {
    return {
      activeTab: 'quota'
    }
  },
  mounted () {
    bus.$emit('set-topbar-title', { title: '系统配置', breadcrumb: [] })
    bus.$emit('set-sub-sidebar-title', {
      title: '',
      routerList: []
    })
  }
}
</script>

<style lang="less" >
.config-home {
  position: relative;
  flex: 1;
  padding: 15px 30px;
  overflow: auto;
}
</style>
