<template>
  <v-flex>
    <v-text-field
      label="Сообщение"
      v-model="text"
      @keydown.enter="send"
      filled
    ></v-text-field>
  </v-flex>
</template>

<script>
export default {
  data: () => ({
    text: ''
  }),
  methods: {
    send() {
      this.$socket.emit('createMessage', {
        text: this.text,
        id: this.$store.state.user.id
      }, data => {
        if (typeof data === 'string') {
          console.error(data);
        } else {
          this.text = ''
        }
      })
    }
  }
}
</script>
