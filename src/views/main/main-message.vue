<template>
  <div>
    <el-popover v-if="msgTotal > 0" placement="bottom" trigger="hover" width="350">
      <el-badge :value="msgTotal" slot="reference">
        <i class="el-icon-bell"></i>
      </el-badge>
      <div>
        <el-tabs v-model="activeName" @tab-click="handleClick">
          <el-tab-pane :label="msg1Title" name="first">
            <div v-for="record in noticeMsgContentList" :key="record.id">
              <el-row>
                <el-col :span="18">
                  <div style="margin-left: 5%;width: 80%">
                    <p><a @click="updateNoticeStatus(record)" style="display: inline-block;cursor: pointer;">{{ record.title }}</a></p>
                    <p style="color: rgba(0,0,0,.45);margin-bottom: 0px">{{ record.createTime }} 发布</p>
                  </div>
                </el-col>
                <el-col :span="6">
                  <div style="text-align: right">
                    <el-tag v-if="record.priority === 'L'" @click="updateNoticeStatus(record)">一般消息</el-tag>
                    <el-tag v-if="record.priority === 'M'" type="warning" @click="updateNoticeStatus(record)">重要消息</el-tag>
                    <el-tag v-if="record.priority === 'H'" type="danger" @click="updateNoticeStatus(record)">紧急消息</el-tag>
                  </div>
                </el-col>
              </el-row>
              <el-divider />
            </div>
            <div style="margin-top: 5px;text-align: center">
              <el-button type="dashed" block @click="showMoreNotice()">查看更多</el-button>
            </div>
          </el-tab-pane>
          <el-tab-pane :label="msg2Title" name="second">
            <div v-for="record in sysMsgConnectList" :key="record.id">
              <el-row>
                <el-col :span="18">
                  <div style="margin-left: 5%;width: 80%">
                    <p><a @click="updateNoticeStatus(record)">{{ record.title }}</a></p>
                    <p style="color: rgba(0,0,0,.45);margin-bottom: 0px">{{ record.createTime }} 发布</p>
                  </div>
                </el-col>
                <el-col :span="6">
                  <div style="text-align: right">
                    <el-tag v-if="record.priority === 'L'" @click="updateNoticeStatus(record)">一般消息</el-tag>
                    <el-tag v-if="record.priority === 'M'" type="warning" @click="updateNoticeStatus(record)">重要消息</el-tag>
                    <el-tag v-if="record.priority === 'H'" type="danger" @click="updateNoticeStatus(record)">紧急消息</el-tag>
                  </div>
                </el-col>
              </el-row>
              <el-divider />
            </div>
            <div style="margin-top: 5px;text-align: center">
              <el-button type="dashed" block @click="showMoreNotice()">查看更多</el-button>
            </div>
          </el-tab-pane>
        </el-tabs>
      </div>
    </el-popover>
    <show-notice
      ref="view"
      :dialog-visible="dialog.isVisible"
      :title="dialog.title"
      :type="dialog.type"
      @close="viewClose"
      @success="editSuccess"
    />
    <show-more-notice
      ref="viewMore"
      :dialog-visible="dialogMore.isVisible"
      :title="dialogMore.title"
      :type="dialogMore.type"
      @close="viewMoreClose"
      @success="editSuccess"
    />
  </div>
</template>

<script>
import ShowNotice from './ShowNotice'
import ShowMoreNotice from './ShowMoreNotice'
export default {
  name: 'MainMessage',
  data () {
    return {
      dialog: {
        isVisible: false,
        title: '',
        type: ''
      },
      dialogMore: {
        isVisible: false,
        title: '',
        type: ''
      },
      activeName: 'first',
      noticeMsgContentList: [],
      sysMsgConnectList: [],
      msg1Title: '通知公告(0)',
      msg2Title: '系统消息(0)',
      msg1Count: '0',
      msg2Count: '0',
      msgWebSocket: null
    }
  },
  components: {
    ShowNotice,
    ShowMoreNotice
  },
  computed: {
    userId: {
      get () { return this.$store.state.user.id }
    },
    msgTotal () {
      return parseInt(this.msg1Count) + parseInt(this.msg2Count)
    }
  },
  mounted () {
    this.loadData()
    this.initWebSocket()
  },
  destroyed () {
    this.websocketOnclose()
  },
  methods: {
    handleClick (tab, event) {
      console.log(tab, event)
    },
    editSuccess () {
      this.loadData()
    },
    viewClose () {
      this.dialog.isVisible = false
    },
    viewMoreClose () {
      this.dialogMore.isVisible = false
    },
    view (row) {
      this.$refs.view.setNotice(row)
      this.dialog.title = '查看'
      this.dialog.isVisible = true
      this.dialog.type = 'view'
    },
    loadData () {
      this.$http({
        url: this.$http.adornUrl(`/notice/listNoticeByUser`),
        method: 'get',
        params: this.$http.adornParams()
      }).then(({data}) => {
        if (data && data.code === 0) {
          this.noticeMsgContentList = data.data.noticeMsgList
          this.msg1Count = data.data.noticeMsgTotal
          this.msg1Title = '通知公告(' + data.data.noticeMsgTotal + ')'
          this.sysMsgConnectList = data.data.sysMsgList
          this.msg2Count = data.data.sysMsgTotal
          this.msg2Title = '系统消息(' + data.data.sysMsgTotal + ')'
        }
      })
    },
    updateNoticeStatus (record) {
      this.$http({
        url: this.$http.adornUrl(`/notice/send/editByNoticeIdAndUserId/${record.id}`),
        method: 'get',
        params: this.$http.adornParams()
      }).then(({data}) => {
        if (data && data.code === 0) {
          this.loadData()
        }
      })
      this.view(record)
    },
    showMoreNotice () {
      // console.log(this.activeName)
      this.dialogMore.title = '查看'
      this.dialogMore.isVisible = true
      this.dialogMore.type = 'view'
    },
    initWebSocket: function () {
      // WebSocket与普通的请求所用协议有所不同，ws等同于http，wss等同于https
      const userId = this.userId
      const baseUrl = window.SITE_CONFIG.wsUrl
      const url = baseUrl.replace('https://', 'wss://').replace('http://', 'ws://') + '/ws/common/' + userId
      this.msgWebSocket = new WebSocket(url)
      this.msgWebSocket.onopen = this.websocketOnopen
      this.msgWebSocket.onerror = this.websocketOnerror
      this.msgWebSocket.onmessage = this.websocketOnmessage
      this.msgWebSocket.onclose = this.websocketOnclose
    },
    websocketOnopen: function () {
      console.log('WebSocket连接成功')
    },
    websocketOnerror: function (e) {
      console.log('WebSocket连接发生错误')
      this.reconnect()
    },
    websocketOnmessage: function (e) {
      console.log('接收消息', e.data)
      const data = JSON.parse(e.data)
      if (data.cmd === 'topic' || data.cmd === 'user') {
        // 系统通知或用户消息
        this.loadData()
        this.$refs.viewMore.getDataList()
        this.$notify({
          title: data.msgTitle,
          message: data.msgTxt,
          position: 'bottom-right'
        })
      } else if (data.cmd === 'revoke') {
        // 撤销
        this.loadData()
        this.$refs.viewMore.getDataList()
      }
    },
    websocketOnclose: function (e) {
      console.log('connection closed', e)
      if (e) {
        console.log('connection closed', e.code)
      }
      this.reconnect()
    },
    websocketSend (text) { // 数据发送
      try {
        this.msgWebSocket.send(text)
      } catch (err) {
        console.log('send failed', err.code)
      }
    },
    reconnect () {
      const that = this
      if (that.lockReconnect) return
      that.lockReconnect = true
      // 没连接上会一直重连，设置延迟避免请求过多
      setTimeout(function () {
        console.info('try reconnecting...')
        that.initWebSocket()
        that.lockReconnect = false
      }, 5000)
    }
  }
}
</script>

<style scoped>
</style>
