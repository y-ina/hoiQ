<template>
  <v-app id="inspire">
    <Sidebar />
    <v-app-bar
      app
      shrink-on-scroll
    >

      <v-toolbar-title>ルーム一覧</v-toolbar-title>
      <CreateRoom />

      <v-spacer></v-spacer>

      <v-btn icon>
        <v-icon>mdi-dots-vertical</v-icon>
      </v-btn>
    </v-app-bar>

    <v-main>
      <v-container>
        <v-row>
          <v-col
            v-for="room in rooms"
            :key="room.id"
            cols="4"
          >
            <router-link :to="{ path: '/chat', query: { room_id: room.id }}">
              <v-avatar
                color="grey lighten-2"
                size="128"
              >
                <img
                  src="https://photos.google.com/share/AF1QipNU5pvp2rKJqBUJbYlegcvykawxI2xXOAtAnbrcG_N1uKeUWE0sMIxp3dRkzLc-8Q/photo/AF1QipM-y0cm_2SG3rmSyjYYJxiLzbk2A2Lco_xXGQyR?key=eVZDQTctOEdEMFh4c0hLSncxT3JkaEswZDNISXRn"
                  v-if="!room.thumbnailUrl"
                >
                <img
                  :src="room.thumbnailUrl"
                  v-if="room.thumbnailUrl"
                >
              </v-avatar>
            </router-link>
          </v-col>
          <!-- <v-col
            v-for="n in 24"
            :key="n"
            cols="4"
          >
            <router-link :to="{ path: '/chat', query: { user_id: n }}">
              <v-avatar
                color="grey lighten-2"
                size="128"
              ></v-avatar>
            </router-link>
          </v-col> -->
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import Sidebar from '@/components/layouts/Sidebar'
import CreateRoom from '@/components/modal/CreateRoom'
import firebase from '@/firebase/firebase'

export default {
  components: {
    Sidebar,
    CreateRoom
  },
  data: () => ({
    rooms: []
  }),
  mounted() {
    this.getRooms()
  },
  methods: {
    async getRooms() {
      this.rooms = []
      const roomRef = firebase.firestore().collection("rooms")
      const snapshot = await roomRef.get()
      console.log("snapshot", snapshot)
      snapshot.forEach(doc => {
        const data = {...doc.data()}
        data.id = doc.id
        console.log(data)
        this.rooms.push(data)
      })
      



    }
  }
  
}
</script>