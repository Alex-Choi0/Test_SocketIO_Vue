<script setup>
import { io } from "socket.io-client";
import { onBeforeMount, ref } from "vue";
const socket = io("http://127.0.0.1:5031");
const messages = ref([]);
const messageText = ref("");
const user = ref("");
const room = ref("");
const joined = ref(false);

onBeforeMount(() => {});

const join = () => {
  if (!user.value || !room.value) {
    alert("You need to Enter both user and room");
  } else {
    socket.emit("join", { user: user.value, room: room.value }, () => {
      joined.value = true;
    });

    socket.emit("findAllSocketio", { room: room.value }, (response) => {
      messages.value = response;
    });

    socket.on(room.value, (message) => {
      console.log("socket.on message : ", message);
      messages.value.push(message);
    });
  }
};

const sendMessage = () => {
  console.log("sendMessage : ", room);
  socket.emit(
    "createSocketio",
    { text: messageText.value, room: room.value },
    () => {
      // message.value.push(response);
      messageText.value = "";
    }
  );
};
</script>

<template>
  <div class="chat">
    <div v-if="!joined">
      <form @submit.prevent="join">
        <label>What's your name?</label>
        <br />
        <input v-model="user" />
        <br />
        <label>Which room do you want to join?</label>
        <br />
        <input v-model="room" />
        <button type="submit">Send</button>
      </form>
    </div>
    <div class="chat-container">
      <div class="messages-container">
        <div v-for="messages in messages">
          [{{ messages.name }}]: {{ messages.time }} -> {{ messages.text }}
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
