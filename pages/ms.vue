<template>
  <div v-if="isLoading">
    <span>Now loading...</span>
  </div>
  <div v-else>
    <div v-if="isSignedIn">
      <div>
        <span>サインイン中： {{ user.displayName }} ({{ user.email }}).</span>
      </div>
      <div>
        <button
          :disabled="isLoading"
          @click="signOut"
        >
          Sign out
        </button>
      </div>
    </div>
    <div v-else>
      <div>
        <span>サインインが必要</span>
      </div>
      <div>
        <button
          :disabled="isLoading"
          @click="signIn"
        >
          Microsoftでサインインする！
        </button>
      </div>
    </div>
  </div>
</template>

<script>
// import { auth, firebase } from '~/modules/firebase'
import { auth } from '~/modules/firebase'

export default {
  asyncData () {
    return {
      isLoading: true,
      isSignedIn: false,
      user: {}
    }
  },
  mounted () {
    auth().onAuthStateChanged((user) => {
      this.isLoading = false
      if (user) {
        this.isSignedIn = true
        this.user = user
      } else {
        this.isSignedIn = false
        this.user = {}
      }
    })
  },
  methods: {
    signIn () {
      // if (!firebase.auth().currentUser) {
      if (!auth().currentUser) {
        // [START createprovider]
        // const provider = new firebase.auth.OAuthProvider('microsoft.com')
        const provider = new auth.OAuthProvider('microsoft.com')
        provider.setCustomParameters({
          tenant: '9d51ceb8-50a5-4f1b-ab96-12f3ed17ee78'
        })
        // [END createprovider]
        // [START addscopes]
        provider.addScope('User.Read')
        // [END addscopes]
        // [START signin]
        // firebase.auth().signInWithRedirect(provider)
        auth().signInWithRedirect(provider)
        // [END signin]
      } else {
        // [START signout]
        // firebase.auth().signOut()
        auth().signOut()
        // [END signout]
      }
    },
    signOut () {
      auth().signOut()
    }
  }
}
</script>
