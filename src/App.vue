<template>
  <div>
    <vue-advanced-chat
      height="calc(100vh - 20px)"
      :current-user-id="currentUserId"
      :rooms="JSON.stringify(rooms)"
      :rooms-loaded="true"
      :messages="JSON.stringify(messages)"
      :messages-loaded="messagesLoaded"
      @send-message="sendMessage($event.detail[0])"
      @fetch-messages="fetchMessages($event)"
      @message-action-handler="msgHandler($event)"
      :message-actions="
        JSON.stringify([
          {
            name: 'addMessageToFavorite',
            title: 'Add To Favorite',
          },
          {
            name: 'shareMessage',
            title: 'Share Message',
          },
        ])
      "
    >
      <div v-for="message in messages" :slot="'message_' + message._id">
        <!-- <p class="message-nickname">
          {{ message.date }} {{ message.username }}
        </p> -->
        <p class="message-classic">{{ message.content }}</p>
        <el-select v-model="value" placeholder="Select">
          <el-option
            v-for="item in options"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          >
          </el-option>
        </el-select>
      </div>
    </vue-advanced-chat>
  </div>
</template>

<script>
import { register } from "vue-advanced-chat";
// import { register } from '../../vue-advanced-chat/dist/vue-advanced-chat.es.js'
register();

export default {
  data() {
    return {
      currentUserId: "1234",
      rooms: [
        {
          roomId: "1",
          roomName: "Room 1",
          avatar: "https://66.media.tumblr.com/avatar_c6a8eae4303e_512.pnj",
          users: [
            { _id: "1234", username: "John Doe" },
            { _id: "4321", username: "John Snow" },
          ],
        },
      ],
      messages: [],
      messagesLoaded: false,
      options: [
        {
          value: "Option1",
          label: "Option1",
        },
        {
          value: "Option2",
          label: "Option2",
        },
        {
          value: "Option3",
          label: "Option3",
        },
        {
          value: "Option4",
          label: "Option4",
        },
        {
          value: "Option5",
          label: "Option5",
        },
      ],
    };
  },

  methods: {
    fetchMessages(event) {
      console.log("fetchMsg:", event);
      const { options = {} } = event.detail[0];

      setTimeout(() => {
        if (options.reset) {
          this.messages = this.addMessages(true);
        } else {
          this.messages = [...this.addMessages(), ...this.messages];
          this.messagesLoaded = true;
        }
        // this.addNewMessage()
      });
    },

    addMessages(reset) {
      const messages = [];

      for (let i = 0; i < 30; i++) {
        messages.push({
          _id: reset ? i : this.messages.length + i,
          content: `${reset ? "" : "paginated"} message ${i + 1}`,
          senderId: "4321",
          username: "John Doe",
          date: "13 November",
          timestamp: "10:20",
        });
      }

      return messages;
    },

    sendMessage(message) {
      this.messages = [
        ...this.messages,
        {
          _id: this.messages.length,
          content: message.content,
          senderId: this.currentUserId,
          timestamp: new Date().toString().substring(16, 21),
          date: new Date().toDateString(),
        },
      ];
    },

    addNewMessage() {
      setTimeout(() => {
        this.messages = [
          ...this.messages,
          {
            _id: this.messages.length,
            content: "NEW MESSAGE",
            senderId: "1234",
            timestamp: new Date().toString().substring(16, 21),
            date: new Date().toDateString(),
          },
        ];
      }, 2000);
    },
    msgHandler(event) {
      console.log("msgHandler", event);
    },
  },
};
</script>

<style lang="scss">
body {
  font-family: "Quicksand", sans-serif;
}
.message-classic {
  position: relative;
  left: 3px;
  margin-top: 5px;
  margin-bottom: 2px;
  display: block;
  padding: 9px 12px;
  font-size: 14px;
  // color: #333333;
  border-radius: 5px;
  white-space: pre-line;
  word-break: break-all;
  text-align: left;
  color: #ffffff;
  background-color: rgba(55, 126, 200, 0.8);

  &::before {
    content: "";
    position: absolute;
    border-width: 5px;
    border-style: solid;

    left: -10px;
    // border-color: transparent rgba(255, 255, 255, 0.8) transparent transparent;
    border-color: transparent rgba(55, 126, 200, 0.8) transparent transparent;
  }
}
// BUG: NOT WORKING this way!!!
.vac-offset-current {
  .message-classic::before {
    right: -10px;
    border-color: transparent transparent transparent rgba(55, 126, 200, 0.8);
  }
}
.message-nickname {
  color: #777777;
  font-size: 12px;
}
</style>
