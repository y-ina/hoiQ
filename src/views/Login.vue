<template>
  <v-app>
    <div class="login-box">
      <v-card class="login-form">
        <v-card-title class="login-title">ログイン</v-card-title>
        <v-card-subtitle>ユーザー情報をご入力ください</v-card-subtitle>
        <v-btn text color="light-blue" to="signup">新規登録はこちら</v-btn>
        <v-form
          ref="form"
          v-model="valid"
          lazy-validation
        >

          <v-text-field
            v-model="email"
            :rules="emailRules"
            label="メールアドレス"
            required
          ></v-text-field>

          <v-text-field
            v-model="password"
            :rules="passwordRules"
            type="password"
            label="パスワード"
          >

          </v-text-field>

          <v-btn
          color="success"
          class="login-btn"
          :disabled="isValid"
          >
            ログイン
          </v-btn>

          <v-btn>
            クリア
          </v-btn>

          <v-alert
            dense
            text
            type="success"
            class="success-message"
            v-if="message"
          >
            {{ message }}
          </v-alert>
        </v-form>
      </v-card>
    </div>
  </v-app>
</template>

<script>
  export default {
    data: () => ({
      valid: true,
      email: '',
      emailRules: [
        v => !!v || 'メールアドレスを入力してください。',
        v => /.+@.+\..+/.test(v) || 'メールアドレスが不正です。',
      ],
      password:'',
      passwordRules: [
        v => !!v || 'パスワードを入力してください。',
      ],
      message: '',
    }),

    mounted() {
      if(localStorage.message) {
        this.message = localStorage.message
        localStorage.message = ''
      }
    },

    computed: {
      isValid() {
        console.log("isValid", this.valid);
        return !this.valid;
      }
    },

    methods: {
      validate () {
        this.$refs.form.validate()
      },
      reset () {
        this.$refs.form.reset()
      },
      resetValidation () {
        this.$refs.form.resetValidation()
      },
    },
  }
</script>

<style scoped>
.login-box{
  width: 50%;
  margin: 0px auto;
  padding: 30px;
}
.login-form {
  margin: 150px;
  padding: 30px;
}
.login-title{
  display: inline-block;
}
.login-btn{
  margin-right: 20px;
}
.success-message {
  margin-top: 20px;
}
</style>