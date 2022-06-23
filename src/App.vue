<script setup>
import { io } from "socket.io-client";
import { onBeforeMount, ref } from "vue";
const socket = io("http://localhost:5031");
const messages = ref([]);
const messageText = ref("");
const user = ref("");
const joined = ref(false);

onBeforeMount(() => {
  socket.emit("findAllSocketio", {}, (response) => {
    messages.value = response;
  });

  socket.on("message", (message) => {
    console.log("socket.on message : ", message);
    messages.value.push(message);
  });
});

const join = () => {
  socket.emit("join", { user: user.value }, () => {
    joined.value = true;
  });
};

const sendMessage = () => {
  socket.emit("createSocketio", { text: messageText.value }, () => {
    // message.value.push(response);
    messageText.value = "";
  });
};
</script>

<template>
  <div class="chat">
    <div v-if="!joined">
      <form @submit.prevent="join">
        <label>What's your name?</label>
        <input v-model="user" />
        <button type="submit">Send</button>
      </form>
    </div>
    <div class="chat-container">
      <div class="messages-container">
        <div v-for="messages in messages">
          [{{ messages.name }}]: {{ messages.text }}
        </div>
      </div>
      <hr />
      <div class="message-input">
        <form @submit.prevent="sendMessage">
          <label>Message : </label>
          <input v-model="messageText" @input="emitTyping" />
          <button type="submit">SendChat</button>
        </form>
      </div>
    </div>
  </div>
</template>

<style>
@import "./assets/base.css";
</style>
