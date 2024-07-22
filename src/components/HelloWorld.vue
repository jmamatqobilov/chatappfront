
<template>
  <div>
      <ul>
          <li v-for="message in messages" :key="message.id">
              {{ message.user.name }}: {{ message.content }}
          </li>
      </ul>
      <input type="text" v-model="newMessage" @keyup.enter="sendMessage">
  </div>
</template>

<script>
export default {
  data() {
      return {
          messages: [],
          newMessage: ''
      };
  },
  mounted() {
      this.fetchMessages();
      Echo.private('chat')
          .listen('MessageSent', (e) => {
              this.messages.push(e.message);
          });
  },
  methods: {
      fetchMessages() {
          // Make an API call to fetch messages
          axios.get('/api/messages')
              .then(response => {
                  this.messages = response.data;
              })
              .catch(error => {
                  console.error('Error fetching messages: ', error);
              });
      },
      sendMessage() {
          axios.post('/api/messages', {
              content: this.newMessage
          })
          .then(response => {
              this.newMessage = '';
          })
          .catch(error => {
              console.error('Error sending message: ', error);
          });
      }
  }
}
</script>


<style scoped>
.read-the-docs {
  color: #888;
}
</style>
