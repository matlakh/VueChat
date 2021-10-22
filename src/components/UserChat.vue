<template>
  <div class="chat__container">
    
    <div class="chat__wrapper">
      <div
        v-for="(currentMessage, index) in currentDialog.textMessages"
        :key="index"
      >
        <div
          class="message__data"
          v-if="
            index === 0 ||
            (index > 0 &&
              currentDialog.textMessages[index - 1].data !==
                currentMessage.data)
          "
        >
          --- {{ currentMessage.data }} ---
        </div>
        <div
          class="message__wrapper"
          :class="
            currentDialog.id === currentMessage.authorId
              ? 'u-message'
              : 'm-message'
          "
        >
          <div
            class="message"
            :class="
              currentDialog.id === currentMessage.authorId
                ? 'user__message'
                : 'me__message'
            "
          >
            {{ currentMessage.content }}
            <div
              class="chat__time"
              :class="
                currentDialog.id === currentMessage.authorId
                  ? ''
                  : 'chat__time--left'
              "
            >
              {{ currentMessage.time }}
            </div>
          </div>
        </div>
      </div>
    </div>
<div class="chat__input">
    <input
      type="text"
      placeholder="New message... (press enter to send)"
      @keypress.enter="addMessage"
      class="input"
      v-model="messageInput"
    />
  </div></div>
</template>
<script>
export default {
  props: {
    currentDialog: Object,
  },
  emits: ["addNewMessage"],
  data() {
    return {
      messageInput: "",
      firstMessageSent: false,
    };
  },
  methods: {
    addMessage() {
      this.$emit("addNewMessage", this.messageInput);
      this.messageInput = "";
    },
  },
};
</script>
