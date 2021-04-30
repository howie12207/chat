<template>
  <div class="break-words bg-red-200">
    <div
      class="relative text-center overflow-hidden lg:mx-auto lg:max-w-xl lg:rounded-lg"
    >
      <!-- Header -->
      <section class="h-10 relative">
        <div
          class="absolute py-2 text-base bg-gray-800 text-white w-full flex items-center"
        >
          <router-link to="/" class="w-1/6 flex items-center justify-center"
            ><icon-chevron
          /></router-link>
          <span class="w-4/6">{{ `聊天室 (${count})` }}</span>
          <span class="w-1/6 flex items-center justify-center"
            ><icon-menu :size="24"
          /></span>
        </div>
      </section>
      <section
        ref="chatBox"
        class="chat_box py-4 px-2 bg-gray-900 overflow-x-hidden text-sm overflow-y-auto"
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
            <div class="flex">
              <p
                class="text-white p-2 my-1 bg-gray-700 inline-block rounded max-w-full"
              >
                {{ msg.message }}
              </p>
              <p class="text-gray-500 self-end m-1">
                {{ toTime(msg.createTime) }}
              </p>
            </div>
          </div>
          <!-- 自己發言 -->
          <div
            v-else-if="msg.type === 'message' && msg.id === id"
            class="my-2 w-4/5 ml-auto text-right"
          >
            <p class="text-white">{{ msg.nickname }}</p>
            <div class="flex justify-end">
              <p class="text-gray-500 self-end m-1">
                {{ toTime(msg.createTime) }}
              </p>
              <p class="p-2 my-1 bg-green-600 inline-block rounded max-w-full">
                {{ msg.message }}
              </p>
            </div>
          </div>
        </div>
        <!-- 置底按鈕 -->
        <transition name="move-up">
          <div
            v-show="showToBottom"
            class="absolute bottom-16 right-4 w-8 h-8 flex items-center justify-center rounded-full bg-gray-700 text-white cursor-pointer lg:right-8"
            @click="scrollToBottom"
          >
            <icon-arrow direction="bottom" :size="16" />
          </div>
        </transition>
      </section>
      <!-- 置底留言 -->
      <transition name="move-up">
        <p
          v-show="showLastMessage"
          class="absolute bottom-10 py-1 px-4 bg-gray-700 bg-opacity-50 w-full text-left"
          @click="scrollToBottom"
        >
          {{ last.nickname }}: {{ last.message }}
        </p>
      </transition>
      <!-- Bottom -->
      <section class="h-10 relative">
        <div class="absolute bottom-0 w-full">
          <input
            v-model="inputMessage"
            type="text"
            class="py-2 pl-4 pr-8 w-full focus:outline-none bg-gray-500"
            placeholder="輸入訊息"
            @keyup.enter="submit"
          />
          <img
            class="absolute top-5 right-2 cursor-pointer"
            style="transform: translateY(-50%)"
            src="./submit.svg"
            alt="submit"
            @click="submit"
          />
        </div>
      </section>
    </div>
    <transition name="fade">
      <loading v-if="loading" />
    </transition>
  </div>
</template>

<script>
import loading from "@/components/Loading.vue";
import iconArrow from "@/components/IconArrow.vue";
import iconChevron from "@/components/IconChevron.vue";
import iconMenu from "@/components/IconMenu.vue";
export default {
  components: { loading, iconArrow, iconChevron, iconMenu },
  data() {
    return {
      nickname: sessionStorage.getItem("nickname"),
      websock: null,
      messageList: [],
      inputMessage: "",
      id: null,
      count: null,
      loading: true,
      showToBottom: false,
      showLastMessage: false,
      last: {
        nickname: "",
        message: "",
      },
    };
  },
  mounted() {
    if (!this.nickname) {
      this.$router.replace("/");
      return;
    }
    this.initWebSocket();
    this.$refs.chatBox.addEventListener("scroll", this.scrollHandle);
  },
  beforeDestroy() {
    this.$refs.chatBox.removeEventListener("scroll", this.scrollHandle);
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
        this.scrollToBottom();
      }, 0);
    },
    toTime(timestamp) {
      const day = new Date(timestamp);
      const hh = day.getHours() < 10 ? "0" + day.getHours() : day.getHours();
      const mm =
        day.getMinutes() < 10 ? "0" + day.getMinutes() : day.getMinutes();
      return `${hh}:${mm}`;
    },
    scrollHandle() {
      const el = this.$refs.chatBox;
      if (el.offsetHeight + Math.ceil(el.scrollTop) - el.scrollHeight >= 0) {
        this.showToBottom = false;
        this.showLastMessage = false;
      } else {
        this.showToBottom = true;
      }
    },
    scrollToBottom() {
      this.$refs.chatBox.scrollTop = this.$refs.chatBox.scrollHeight;
    },
    initWebSocket() {
      // const wsPath = `wss://secure-brook-34506.herokuapp.com/${this.nickname}`;
      const wsPath = `ws://localhost:3001/${this.nickname}`;
      this.websock = new WebSocket(wsPath);
      this.websock.onmessage = this.websocketonmessage;
      this.websock.onopen = this.websocketonopen;
      this.websock.onerror = this.websocketonerror;
      this.websock.onclose = this.websocketclose;
    },
    websocketonopen() {
      this.loading = false;
      console.log("已連線");
    },
    websocketonerror() {
      // 连接建立失败重连
      this.loading = true;
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
      if (!this.showToBottom) {
        this.scrollToBottom();
      } else if (receive?.type === "message" && receive?.id !== this.id) {
        this.showLastMessage = true;
        this.last.nickname = receive.nickname;
        this.last.message = receive.message;
      }
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
.chat_box {
  min-height: calc(100vh - 5rem);
  max-height: calc(100vh - 5rem);
}
</style>
