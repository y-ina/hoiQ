<template>
  <v-navigation-drawer
      v-model="drawer"
      app
    >
      <v-sheet
        color="grey lighten-4"
        class="pa-4"
      >
        <v-avatar color="indigo">
          <input 
              type="file"
              ref="fileInput" 
              accept="image/jpeg, image/jpg, image/png,"
              style="display: none"
              @change="updateIcon"
            >
          <v-icon 
              dark
              @click="changeIcon"
            >
            mdi-account-circle
          </v-icon>
        </v-avatar>

        <div class="username">{{ auth && auth.displayName }}</div>
      </v-sheet>

      <v-divider></v-divider>

      <v-list>
        <v-list-item
          v-for="[icon, text, to] in links"
          :key="icon"
          :to="to"
          link
        >
          <v-list-item-icon>
            <v-icon>{{ icon }}</v-icon>
          </v-list-item-icon>

          <v-list-item-content>
            <v-list-item-title>{{ text }}</v-list-item-title>
          </v-list-item-content>
        </v-list-item>

        <v-list-item @click="logout">
          <v-list-item-icon>
            <v-icon>
              mdi-logout
            </v-icon>
          </v-list-item-icon>

          <v-list-item-content>
            <v-list-item-title>ログアウト</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
</template>

<script>
  import firebase from "@/firebase/firebase"

  export default {
    mounted() {
      this.auth = JSON.parse(sessionStorage.getItem('user'))
    },
    data: () => ({
      drawer: null,
      links: [
        ['mdi-inbox-arrow-down', 'Inbox', '/'],
        ['mdi-send', 'Send', '/about'],
        ['mdi-delete', 'Trash', '/about'],
        ['mdi-alert-octagon', 'Spam', '/about'],
      ],
      auth: null
    }),
    methods: {
      logout () {
        console.log("call logout")
        firebase.auth()
            .signOut()
            .then(() => {
              localStorage.message = "ログアウトに成功しました。";
              this.$router.push('/login')
            })
            .catch((error) => {
            console.log("失敗", error)
            this.errorMessage = "ログアウトに失敗しました。";
          })
      },
      changeIcon() {
        this.$refs.fileInput.click()
      },
      updateIcon() {
        console.log("call updateIcon")
        const user = this.getAuth()
        if(!user) {
          sessionStorage.removeItem('user')
          this.$router.push('/login')
        }

        const file = this.$refs.fileInput.files[0]
        const filePath = '/user/${file.name}'
        console.log("callfailes", file)

        firebase.storage().ref()
          .child(filePath)
          .put(file)
          .then(snapshot => {
            console.log("snapshot", snapshot)
        });
      },
      getAuth() {
        return firebase.auth().onAuthStateChanged((user) => {
          return user
        })
      },
    }
  }
</script>