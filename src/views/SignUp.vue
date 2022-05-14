<template>
  <v-app>
    <div class="signup-box">
      <v-card class="signup-form">
        <v-card-title class="signup-title">新規登録</v-card-title>
        <v-card-subtitle>ユーザー情報をご入力ください</v-card-subtitle>
        <v-btn text color="light-blue" to="login">ログインはこちら</v-btn>
        <v-form
          ref="form"
          v-model="valid"
          lazy-validation
        >

          <v-text-field
            v-model="name"
            :rules="nameRules"
            label="名前"
            required
          ></v-text-field>

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
          class="signup-btn"
          @click="signup"
          :disabled="isValid"
          >
            新規登録
          </v-btn>

          <v-btn>
            クリア
          </v-btn>

          <v-alert
            dense
            outlined
            type="error"
            class="error-message"
            v-if="errorMessage"
          >
          {{ errorMessage }}
          </v-alert>

        </v-form>
      </v-card>
    </div>
  </v-app>
</template>

<script>
  import firebase from "@/firebase/firebase"

  export default {
    data: () => ({
      valid: true,
      name: '',
      nameRules: [
        v => !!v || '名前を入力してください。',
        v => (v && v.length <= 10) || '名前は10文字以内で入力してください。',
      ],
      email: '',
      emailRules: [
        v => !!v || 'メールアドレスを入力してください。',
        v => /.+@.+\..+/.test(v) || 'メールアドレスが不正です。',
      ],
      password:'',
      passwordRules: [
        v => !!v || 'パスワードを入力してください。',
        v => (v && v.length >= 6) || 'パスワードは6文字以上で作成してください。',
      ],
      errorMessage: "",
    }),

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
      signup (){
        // 新規登録処理
        console.log("signupcall")
        firebase.auth()
        .createUserWithEmailAndPassword(this.email, this.password)
        .then(async (result) => {
          console.log("success", result);
          await result.user.updateProfile(
            {displayName: this.name}
          );
          console.log("UpdateUser", result.user);
          localStorage.message = "新規登録に成功しました。"
          // 成功時TOP画面にリダイレクト
          this.$router.push('/login')
        })
        .catch((error) => {
          console.log("fail", error)
          // 失敗時エラーメッセージを表示
          this.errorMessage = "ユーザーの新規作成に失敗しました。";
          
        })
      }
    },
  }
</script>

<style scoped>
.signup-box{
  width: 50%;
  margin: 0px auto;
  padding: 30px;
}
.signup-form {
  margin: 150px;
  padding: 30px;
}
.signup-title {
  display: inline-block;
}
.signup-btn {
  margin-right: 20px;
}

.error-message {
  margin-top: 20px;
}
</style>