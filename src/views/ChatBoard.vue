<template>
  <v-app id="inspire">
    <Sidebar />
    <v-main>
      <h1>{{ room? room.name : "" }}</h1>
      <v-container
        class="py-8 px-6"
        fluid
      >
        <v-row>
          <v-col
            v-for="card in cards"
            :key="card"
            cols="12"
          >
            <v-card>
              <v-subheader>{{ card }}</v-subheader>

              <v-list two-line>
                <template v-for="(data, index) in messages">
                  <v-list-item

                    :key="index"
                  >
                    <v-list-item-avatar color="grey darken-1">
                    </v-list-item-avatar>

                    <v-list-item-content>
                      <!-- <v-list-item-title>Message {{ n }}</v-list-item-title> -->

                      <v-list-item-subtitle class="message">
                        {{ data.message }}
                      </v-list-item-subtitle>
                    </v-list-item-content>
                  </v-list-item>

                  <v-divider
                    v-if="index !== 6"
                    :key="`divider-${index}`"
                    inset
                  ></v-divider>
                </template>
              </v-list>
            </v-card>
          </v-col>
        </v-row>
      </v-container>

      <v-textarea
          v-model="body"
          class="mx-2"
          label="メッセージを入力"
          rows="3"
          prepend-icon="mdi-comment"
          auto-grow
      ></v-textarea>

      <v-btn @click="submit"
        class="mr-4"
        type="submit"
        :disabled="invalid"
      >
        submit
      </v-btn>
      <v-btn @click="clear">
        clear
      </v-btn>

    </v-main>
  </v-app>
</template>

<script>
import firebase from "@/firebase/firebase"
import Sidebar from '@/components/layouts/Sidebar'

export default {
  components: {
    Sidebar
  },
  async created() {
    this.roomId = this.$route.query.room_id;
    console.log("room_id", this.roomId);

    const roomRef = firebase.firestore().collection("rooms").doc(this.roomId)
    const roomDoc = await roomRef.get()
    if(!roomDoc.exists) {
      await this.$router.push('/')
    }
    this.room = roomDoc.data()

    const snapshot = await roomRef.collection('messages').get()
    snapshot.forEach(doc => {
      console.log('data', doc.data())
      this.messages.push(doc.data())
    })
    
    
    // const snapshot = await chatRef.get()
    // console.log('snapshot', snapshot);

    // snapshot.forEach(doc => {
    //   console.log('data', doc.data());
    //   this.messages.push(doc.data())
    // })
  },
  data: () => ({
    messages: [
      // {message: "message1"},
      // {message: "message2"},
      // {message: "message3"},
      // {message: "message4"},
      // {message: "message5"},
    ],
    body: '',
    room_id: '',
    room: null,
    cards: ['Today'],
    drawer: null,
    links: [
      ['mdi-inbox-arrow-down', 'Inbox'],
      ['mdi-send', 'Send'],
      ['mdi-delete', 'Trash'],
      ['mdi-alert-octagon', 'Spam'],
    ],
    // invalid: false,
  }),
  computed: {
    // 改行でもfalseになるため修正が必要
    invalid() {
      if(!this.body) {
        return true;
      }
      return false;
    }
  },
  methods: {
    clear() {
      console.log('clear');
      this.body = "";
    },
    submit() {
      console.log('submit', this.body);
      this.messages.unshift({message: this.body});
      this.body = "";
    }
  }
}
</script>

<style scoped>
.message {
  text-align: left;
}

</style>