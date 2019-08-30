<template>
  <div class="chat">
    <el-container>
      <el-header>
        <el-menu
          :default-active="activeIndex2"
          class="el-menu-demo"
          mode="horizontal"
          @select="handleSelect"
          background-color="#545c64"
          text-color="#fff"
          active-text-color="#ffd04b"
        >
          <el-menu-item index="1">消息中心</el-menu-item>
        </el-menu>
      </el-header>
      <el-main>
        <div class="main">
          <ul>
            <li v-for="(item, index) in msgList" :key="index">{{item}}</li>
          </ul>
        </div>
      </el-main>
      <el-footer>
        <el-input placeholder="请输入内容" v-model="input2" @keyup.enter.native="sendMsg">
          <el-button @click="sendMsg" slot="append">发送</el-button>
        </el-input>
      </el-footer>
    </el-container>
  </div>
</template>

<script>
export default {
  name: "chat",
  data() {
    return {
      input2: "", //输入要发言的内容
      activeIndex: "1",
      activeIndex2: "1",
      socket: "", //socket实例
      msgList: [] //所有的消息列表
    };
  },
  methods: {
    initSocket() {
      this.socket = new WebSocket("ws://192.168.50.221:12345/zhao");
      this.socket.onmessage = this.socketMessage;
      this.socket.onopen = this.socketOpen;
      this.socket.onclose = this.socketClose;
    },
    socketMessage(msg) {
      console.log("socket接收到的信息", msg.data);
      this.msgList.unshift(JSON.parse(msg.data));
    },
    socketOpen() {
      // setInterval(()=>{
      //   this.socket.send(JSON.stringify('BEAT'))
      // },10*1000)
    },
    socketClose() {
      console.log("断了需要重连");
      // this.socket = new WebSocket("ws://10.0.0.28:12345/zhao");
      this.initSocket();
    },
    getMsg() {
      console.log("点击按钮");
      axios
        .get("http://192.168.50.221:12345/getmsg")
        .then(data => console.log(data));
    },
    // 发送消息
    sendMsg() {
      console.log("发送消息");
      this.socket.send(JSON.stringify(this.input2));
      // axios.get(`http://localhost:12345/sentmsg?msg=${this.input2}`).then(data => console.log(data))
    },
    handleSelect(key, keyPath) {
      console.log(key, keyPath);
    }
  },
  created() {
    this.initSocket();
  }
};
</script>

<style scoped>
.chat {
  width: 1000px;
  margin: 20px auto;
}
.main {
  background: #dcdcdc;
  height: 400px;
  overflow: auto;
}
.main li {
  padding: 10px;
}
</style>
