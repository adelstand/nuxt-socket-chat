<template>
  <v-layout column justify-center align-center>
    <v-flex xs12 sm8>
      <v-card min-width="400px">
        <v-snackbar
          v-model="snackbar"
          :timeout="5000"
        >
          {{ snackbarMessage }}
          <v-btn
            color="pink"
            text
            @click="snackbar = false"
          >
            Закрыть
          </v-btn>
        </v-snackbar>
        <v-card-title>
          <h1>Nuxt chat</h1>
        </v-card-title>
        <v-card-text></v-card-text>
        <v-card-text>
          <v-form
            ref="form"
            v-model="valid"
            :lazy-validation="lazy"
          >
            <v-text-field
              v-model="name"
              :counter="16"
              :rules="nameRules"
              label="Ваше имя"
              required
            ></v-text-field>

            <v-text-field
              v-model="room"
              :rules="roomRules"
              label="Введите id комнаты"
              required
            ></v-text-field>

            <v-btn
              color="primary"
              class="mr-4"
              @click="submit"
            >
              Войти
            </v-btn>
          </v-form>
        </v-card-text>
      </v-card>
    </v-flex>
  </v-layout>
</template>

<script>
import { mapMutations } from 'vuex'

export default {
  layout: 'empty',
  head: {
    title: 'Добро пожаловать в Nuxt Chat'
  },
  sockets: {
    connect() {
      console.log('Client IO connected');
    }
  },
  data: () => ({
    valid: true,
    name: '',
    nameRules: [
      v => !!v || 'Введите имя',
      v => (v && v.length <= 16) || 'Имя не должно быть длиннее 16 символов',
    ],
    room: '',
    roomRules: [
      v => !!v || 'Введите id комнаты',
    ],
    lazy: false,
    snackbar: false,
  }),

  methods: {
    ...mapMutations(['setUser']),
    submit () {
      if (this.$refs.form.validate()) {
        const user = {
          name: this.name,
          room: this.room,
        }

        this.$socket.emit('userJoined', user, data => {
          if (typeof data === 'string') {
            console.error(data)
          } else {
            user.id = data.userID
            this.setUser(user)
            this.$router.push('/chat')

          }
        })
      }
    },
  },
  computed: {
    snackbarMessage() {
      if (this.$route.query.message) {
        this.snackbar = true
        switch (this.$route.query.message) {
          case 'leftChat':
            return 'Вы покинули чат'
          case 'noUser':
            return 'Нет пользователей'
            break
          default:
            return ''
            break
        }
      } else {
        this.snackbar = false
      }
    },
  }
}
</script>
