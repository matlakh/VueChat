<template>
<div class="container">
  <div class="chat-list">
    <div class="list__search">
      <input
        v-model="search"
        class="chat-list_search"
        type="text"
        placeholder="Search..."
      />
    </div>
    <user
      v-for="person in searchPersons"
      :key="person.id"
      :person="person"
      :isSelected="person.id === selectedUserIndex"
      @selected="selectedUser"
      :lastMessage="getLastMessage(person.id)"
    ></user>
  </div>

  <div class="chat">
    <div v-if="selectedUserIndex > -1">
      <chat-header :person="person"> </chat-header>
      <user-chat
        :userId="person.id"
        @addNewMessage="addMessage"
        :currentDialog="dialog"
      ></user-chat>
    </div>
  </div>
</div>
</template>

<script>
import User from "./components/User.vue";
import ChatHeader from "./components/ChatHeader.vue";
import UserChat from "./components/UserChat.vue";

export default {
  components: {
    User,
    ChatHeader,
    UserChat,
  },
  data() {
    return {
      persons: [],
      userDialogs: [
        {
          id: -1,
          textMessages: [
            {
              content: "",
              data: "",
              time: "",
              authorId: -1,
            },
          ],
        },
      ],
      search: "",
      selectedUserIndex: -1,
      firstMessageSent: false,
    };
  },
  methods: {
    async loadJson(callback, fileName) {
      const response = await fetch(fileName, {
        method: "GET",
        headers: { "Content-Type": "application/json" },
      });
      callback(await response.json());
    },
    selectedUser(id) {
      this.selectedUserIndex = id;
    },
    getDate() {
      let date = new Date();
      return (
        (date.getDate() < 10 ? "0" : "") +
        date.getDate() +
        "-" +
        (date.getMonth() + 1 < 10 ? "0" : "") +
        (date.getMonth() + 1) +
        "-" +
        date.getFullYear()
      );
    },
    getTime() {
      let date = new Date();
      return (
        (date.getHours() < 10 ? "0" : "") +
        date.getHours() +
        ":" +
        (date.getMinutes() < 10 ? "0" : "") +
        date.getMinutes() +
        ":" +
        (date.getSeconds() < 10 ? "0" : "") +
        date.getSeconds()
      );
    },
    getLastMessage(userId) {
      let currentDialogIndex = this.userDialogs.findIndex(
        (dialog) => dialog.id === userId
      );
      let currentDialog = this.userDialogs[currentDialogIndex];
      return currentDialog.textMessages[currentDialog.textMessages.length - 1];
    },

    async addMessage(message) {
      if (message.length != 0) {
        let currentIndex = this.userDialogs.findIndex(
          (dialog) => dialog.id === this.selectedUserIndex
        );
        let currentDialog = this.userDialogs[currentIndex].textMessages;
        let apiUrl = "https://api.adviceslip.com/advice";

        currentDialog.push({
          content: message,
          data: this.getDate(),
          time: this.getTime(),
          authorId: 0,
        });

        if (Math.floor(Math.random() * 3) === 1) {
          const response = await fetch(apiUrl, {
            method: "GET",
            headers: { "Content-Type": "text/plain" },
          });
          let textResponse = await response.json();
          currentDialog.push({
            content: textResponse.slip.advice,
            authorId: this.selectedUserIndex,
            time: this.getTime(),
            data: this.getDate(),
          });
        }
      }
    },
  },

  async created() {
    let that = this;

    await that.loadJson(function (data) {
      that.userDialogs = data;
    }, "/data/dialogPersons.json");

    await that.loadJson(function (data) {
      that.persons = data.persons;
    }, "/data/persons.json");
  },
  computed: {
    searchPersons: {
      get() {
        if (this.userDialogs.length > 0) {
          var comp = this.search.toLowerCase();
          let that = this;
          return this.persons
            .filter(function (elem) {
              if (comp === "") {
                return true;
              } else {
                let customNameLowerCase = elem.customName.toLowerCase();
                return customNameLowerCase.indexOf(comp) > -1;
              }
            })
            .sort(function (d1, d2) {
              let indexDialogOne = that.userDialogs.findIndex(
                (dialog) => dialog.id === d1.id
              );
              let indexDialogTwo = that.userDialogs.findIndex(
                (dialog) => dialog.id === d2.id
              );
              let currentDialogOne = that.userDialogs[indexDialogOne];
              let currentDialogTwo = that.userDialogs[indexDialogTwo];
              let dialog1 =
                currentDialogOne.textMessages[
                  currentDialogOne.textMessages.length - 1
                ];
              let dialog2 =
                currentDialogTwo.textMessages[
                  currentDialogTwo.textMessages.length - 1
                ];

              return dialog1.time < dialog2.time ? 1 : -1;
            });
        }
      },
    },
    person: {
      get() {
        let currentIndex = this.persons.findIndex(
          (person) => person.id === this.selectedUserIndex
        );
        return this.persons[currentIndex];
      },
    },
    dialog: {
      get() {
        let currentIndex = this.userDialogs.findIndex(
          (dialog) => dialog.id === this.selectedUserIndex
        );
        return this.userDialogs[currentIndex];
      },
    },
  },
};
</script>