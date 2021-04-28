<template>
  <div class="bg-red-100 py-10 min-h-screen break-words">
    <div
      class="mx-auto my-10 w-80 text-center bg-gray-300 rounded overflow-hidden"
    >
      <div class="py-2">{{ `聊天室 (${count})` }}</div>
      <div
        ref="chatBox"
        class="h-80 py-4 px-2 bg-gray-900 overflow-x-hidden text-sm overflow-y-auto"
      >
        <div v-for="(msg, index) of messageList" :key="index">
          <p
            v-if="msg.type === 'in' || msg.type === 'out'"
            class="bg-blue-100 w-3/5 mx-auto text-center rounded-xl p-2 my-2"
          >
            {{ `${msg.nickname} ${msg.message}` }}
          </p>
          <!-- 他人發言 -->
          <div
            v-else-if="msg.type === 'message' && msg.id !== id"
            class="my-2 w-4/5 text-left"
          >
            <p class="text-white">{{ msg.nickname }}</p>
            <p
              class="text-white p-2 my-1 bg-gray-700 inline-block rounded max-w-full"
            >
              {{ msg.message }}
            </p>
          </div>
          <!-- 自己發言 -->
          <div
            v-else-if="msg.type === 'message' && msg.id === id"
            class="my-2 w-4/5 ml-auto text-right"
          >
            <p class="text-white">{{ msg.nickname }}</p>
            <p
              class="text-white p-2 my-1 bg-gray-700 inline-block rounded max-w-full"
            >
              {{ msg.message }}
            </p>
          </div>
        </div>
        <br />
      </div>
      <div class="relative">
        <input
          v-model="inputMessage"
          type="text"
          class="py-2 px-6 w-80 focus:outline-none"
          @keyup.enter="submit"
        />
        <img
          class="absolute top-5 right-1 cursor-pointer"
          style="transform: translateY(-50%)"
          src="./submit.svg"
          alt=""
          @click="submit"
        />
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      nickname: localStorage.getItem("nickname"),
      websock: null,
      messageList: [],
      inputMessage: "",
      id: null,
      count: null,
    };
  },
  mounted() {
    // this.connect()
    this.initWebSocket();
  },
  beforeDestroy() {
    this.websock.close();
  },
  methods: {
    submit() {
      if (!this.inputMessage.trim()) return;
      this.websocketsend(
        JSON.stringify({
          id: this.id,
          nickname: this.nickname,
          message: this.inputMessage,
          type: "message",
        }),
      );
      this.inputMessage = "";
      setTimeout(() => {
        this.$refs.chatBox.scrollTop = this.$refs.chatBox.scrollHeight;
      }, 0);
    },
    initWebSocket() {
      const wsPath = `wss://secure-brook-34506.herokuapp.com/${this.nickname}`;
      // const wsPath = `ws://localhost:3001/${this.nickname}`;
      this.websock = new WebSocket(wsPath);
      this.websock.onmessage = this.websocketonmessage;
      this.websock.onopen = this.websocketonopen;
      this.websock.onerror = this.websocketonerror;
      this.websock.onclose = this.websocketclose;
    },
    websocketonopen() {
      console.log("已連線");
    },
    websocketonerror() {
      // 连接建立失败重连
      this.initWebSocket();
    },
    websocketonmessage(e) {
      // 数据接收
      const receive = JSON.parse(e.data);
      if (receive?.type === "id") {
        this.id = receive.message;
        return;
      } else if (receive?.type === "count") {
        this.count = receive.message;
        return;
      }
      this.messageList.push(receive);
    },
    websocketsend(Data) {
      // 数据发送
      this.websock.send(Data);
    },
    websocketclose(e) {
      // 关闭
      console.log("已斷線", e);
    },
  },
};
</script>

<style>
.container {
  color: #333;
}
</style>
