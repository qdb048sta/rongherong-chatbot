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
      <div v-for="message in messages" :slot="'message_' + message._id" class="message-container">
        <p class="message-classic">{{ message.content }}</p>
        <div class="message-actions">
          <div class="buttons">
            <el-button type="text" @click="handleYesClick">Yes</el-button>
            <el-button type="text" @click="handleNoClick">No</el-button>
          </div>
          <el-select v-model="selectedOption" placeholder="Select" class="dropdown" @change="handleSelectChange">
            <el-option
              v-for="item in options"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </div>
      </div>
    </vue-advanced-chat>
  </div>
</template>

<script>
import axios from 'axios';
import { register } from "vue-advanced-chat";
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
      selectedOption: null,
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

    msgHandler(event) {
      console.log("msgHandler", event);
    },

    async handleYesClick() {
      console.log("Yes button clicked");
      try {
        const response = await axios.post('/your-api-endpoint', { action: 'yes' });
        console.log('Response:', response.data);
      } catch (error) {
        console.error('Error sending yes request:', error);
      }
    },

    async handleNoClick() {
      console.log("No button clicked");
      try {
        const response = await axios.post('/your-api-endpoint', { action: 'no' });
        console.log('Response:', response.data);
      } catch (error) {
        console.error('Error sending no request:', error);
      }
    },

    async handleSelectChange(value) {
      console.log("Option selected:", value);
      try {
        const response = await axios.post('/your-api-endpoint', { selectedOption: value });
        console.log('Response:', response.data);
      } catch (error) {
        console.error('Error sending select request:', error);
      }
    },
  },
};
</script>

<style lang="scss">
body {
  font-family: "Quicksand", sans-serif;
}

.message-container {
  margin-bottom: 10px; // Add some space between messages if needed
}

.message-classic {
  position: relative;
  left: 3px;
  margin-top: 5px;
  margin-bottom: 2px;
  display: block;
  padding: 9px 12px;
  font-size: 14px;
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
    border-color: transparent rgba(55, 126, 200, 0.8) transparent transparent;
  }
}

.message-actions {
  display: flex;
  flex-direction: column; /* Arrange elements in a column */
  align-items: center; /* Center the content horizontally */
  margin-top: 10px; /* Add space between the message and actions */
}

.buttons {
  display: flex;
  gap: 10px; /* Space between the buttons */
  margin-bottom: 10px; /* Space between buttons and dropdown */
}

.dropdown {
  width: 150px; /* Adjust width if needed */
}

.message-nickname {
  color: #777777;
  font-size: 12px;
}
</style>
