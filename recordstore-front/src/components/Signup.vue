<template>
  <div>
    <div>
      <h3>Sign Up</h3>
      <form @submit.prevent="signup">
        <div v-if="error">{{ error }}></div>

        <div>
          <label for="email">E-mail Address</label>
          <input type="email" v-model="email" id="email" placeholder="email@email.com">
        </div>

        <div>
          <label for="password">Password</label>
          <input type="password" v-model="password" id="password">
        </div>

        <div>
          <label for="password">Password Confirmation</label>
          <input type="password" v-model="password_confirmation" id="password_confirmation">
        </div>

        <button type="submit">Sign Up</button>

        <div>
          <router-link to="/">Sign In</router-link>
        </div>

      </form>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Signup',
  data () {
    return {
      email: '',
      password: '',
      password_confirmation: '',
      error: ''
    }
  },
  created () {
    this.checkSignedIn()
  },
  updated () {
    this.checkSignedIn()
  },
  methods: {
    signin () {
      this.$http.plain.post('/signup', { email: this.email, password: this.password, password_confirmation: this.password_confirmation })
        .then(response => this.signinSuccessful(response))
        .catch(error => this.signinFailed(error))
    },
    signinSuccessful (response) {
      if (!response.data.csrf) {
        this.signinFailed(response)
        return
      }

      localStorage.csrf = response.data.csrf
      localStorage.signedIn = true
      this.error = ''
      this.$router.replace('/records')
    },
    signinFailed (error) {
      this.error = (error.response && error.response.data && error.response.data.error) || 'Something went wrong'
      delete localStorage.csrf
      delete localStorage.signedIn
    },
    checkSignedIn () {
      if (localStorage.signedIn) {
        this.$router.replace('/records')
      }
    }
  }
}
</script>
