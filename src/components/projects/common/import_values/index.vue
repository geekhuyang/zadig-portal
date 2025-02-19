<template>
  <div class="values-outer">
    <h4>
      <span>values 文件</span>
      <el-button type="text" class="title-btn" @click="showGitImportDialog = true">从 Git 导入</el-button>
      <i v-if="showDelete" class="el-icon-delete-solid icon-delete" @click="closeValueEdit"></i>
    </h4>
    <el-divider></el-divider>
    <Resize class="desc mirror" :resize="setResize.direction" :height="setResize.height" @sizeChange="$refs.codemirror.refresh()">
      <codemirror ref="codemirror" v-model="importRepoInfoUse.overrideYaml"></codemirror>
    </Resize>
    <el-dialog title="从 Git 仓库导入" :visible.sync="showGitImportDialog" append-to-body>
      <Repository ref="valueRepoRef" :repoSource="importRepoInfoUse.gitRepoConfig"></Repository>
      <div slot="footer">
        <el-button @click="showGitImportDialog = false" size="small">取 消</el-button>
        <el-button type="primary" @click="importOverrideYaml" size="small" :loading="loadValueYamls">导 入</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import Resize from '@/components/common/resize'
import Codemirror from '../codemirror.vue'
import Repository from './repository.vue'
import { cloneDeep } from 'lodash'
import { getValuesYamlFromGitAPI } from '@api'

const valueInfo = {
  yamlSource: '', // freeEdit or default(不上传)
  overrideYaml: '',
  gitRepoConfig: {
    codehostID: null,
    owner: '',
    repo: '',
    branch: '',
    valuesPaths: []
  }
}

export default {
  data () {
    return {
      showGitImportDialog: false,
      loadValueYamls: false
    }
  },
  props: {
    showDelete: {
      default: false,
      type: Boolean
    },
    resize: {
      type: Object,
      default: () => {
        return {
          height: '260px',
          direction: 'none'
        }
      }
    },
    importRepoInfo: Object
  },
  computed: {
    setResize () {
      return {
        height: '260px',
        direction: 'none',
        ...this.resize
      }
    },
    importRepoInfoUse: {
      get () {
        let gitRepoConfig = {}
        if (!this.importRepoInfo.gitRepoConfig) {
          gitRepoConfig = { gitRepoConfig: cloneDeep(valueInfo.gitRepoConfig) }
        }
        return Object.assign(this.importRepoInfo, gitRepoConfig)
      },
      set (val) {
        this.$emit('update:importRepoInfo', val)
        return val
      }
    }
  },
  methods: {
    importOverrideYaml () {
      this.$refs.valueRepoRef.validate().then(
        async () => {
          this.loadValueYamls = true
          const res = await getValuesYamlFromGitAPI(this.importRepoInfoUse.gitRepoConfig).catch(error =>
            console.log(error)
          )
          this.loadValueYamls = false
          if (res) {
            this.showGitImportDialog = false
            this.importRepoInfoUse.overrideYaml = res
          }
        }
      )
    },
    closeValueEdit () {
      this.importRepoInfoUse.yamlSource = 'default'
      this.$emit('closeValueEdit')
    },
    validate () {
      return Promise.all([Promise.resolve(true)])
    },
    resetValueRepoInfo () {
      this.$refs.valueRepoRef.resetSource()
    }
  },
  components: {
    Codemirror,
    Resize,
    Repository
  }
}
</script>

<style lang="less" scoped>
.values-outer {
  /deep/ .el-divider--horizontal {
    margin: 12px 0;
  }

  padding: 10px;
  line-height: 1;

  .desc-title {
    padding: 6px 0;
  }

  h4 {
    margin: 7px 0;
    color: #606266;
    font-size: 14px;

    .title-btn {
      margin-left: 10px;
      padding: 0;
      font-size: 12px;
    }

    .icon-delete {
      float: right;
      color: #f56c6c;
      font-size: 16px;
      cursor: pointer;
    }
  }

  .desc {
    color: #a1a3a7;
    font-size: 14px;

    .height-40 {
      height: 40px;
      line-height: 40px;
    }

    /deep/.el-input {
      .el-input__inner {
        width: 100%;
      }
    }
  }

  /deep/.el-select {
    width: 100%;
  }
}

/deep/.el-dialog__body {
  padding: 10px 20px;
}
</style>
