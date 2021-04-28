<template>
  <div
    class="bg-gray-200 min-h-screen w-full flex justify-center items-center flex-col"
  >
    <div class="flex flex-col">
      <div class="mb-4">請輸入您的暱稱</div>
      <input
        v-model="nickname"
        type="text"
        class="mb-4 py-2 px-4 rounded w-80 focus:outline-none"
      />
      <div
        class="self-end cursor-pointer ring-2 py-1 px-2 rounded"
        @click="start"
      >
        開始聊天
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      nickname: "",
    };
  },
  mounted() {
    this.nickname = localStorage.getItem("nickname");
  },
  methods: {
    start() {
      this.setNickname();
      this.$router.push("/room");
    },
    setNickname() {
      localStorage.setItem("nickname", this.nickname);
    },
  },
};
</script>
