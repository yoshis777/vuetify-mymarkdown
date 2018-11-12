<template>
  <div id="home">
    <v-btn @click="googleLogin">Googleアカウントでログイン</v-btn>
  </div>
</template>
<script>
  export default {
    name: "home",
    data() {
      return {}
    },
    methods: {
      googleLogin: function() {
        firebase
          .auth()
          .signInWithRedirect(new firebase.auth.GoogleAuthProvider());
      }
    }
  }
</script>
