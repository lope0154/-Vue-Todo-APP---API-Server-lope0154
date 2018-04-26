<template>
  <form class="form box" @submit.prevent="login">
    <h1 class="title is-1">Vue Todos</h1>
    <div v-if="errorMessage" class="errors">{{ errorMessage }}</div>
    <div class="form-group">
      <label for="email">Login name</label>
      <input
        class="input"
        type="email"
        id="email"
        v-model="loginName"
        placeholder="email"
        autocomplete="username"
        required
      >
    </div>
    <div class="form-group">
      <label for="password">Password</label>
      <input
        class="input"
        type="password"
        id="password"
        v-model="password"
        autocomplete="current-password"
        required
      >
    </div>
    <button class="button is-primary" type="submit" @click="login">
      {{ isWorking ? 'Working ...' : 'LOGIN' }}
    </button>
  </form>
</template>

<script>
/* eslint-disable */
import moment from 'moment'
import axios from 'axios'

export default {
  data () {
    return {
      loginName: '',
      password: '',
      errorMessage: '',
      isWorking: false
    }
  },
  methods: {
    login () {
    this.errorMessage = ''
    this.isWorking = true
     axios.post( 'https://vue-todos.robertmckenney.ca/oauth/token',
    {
      grant_type: 'password',
      client_id: '10',
      client_secret: 'N4Mcfrf9OJ38VrXCD2nWp17U9sFsTmKPfwCm9jQM',
      username: this.loginName,
      password: this.password,
      scope: '*'
    }
  )
    .then(response => {
        // eslint-disable-next-line
        const { expires_in, ...rest } = response.data
        const apiTokens = {
        expires_at: moment().add(expires_in, 'seconds').format(),
        ...rest
    }
        this.$emit('saveApiTokens', apiTokens)
        this.loginName = null
        this.password = null
  })
    .catch(error => this.handleError(error))
    .finally(() => { this.isWorking = false })
    },
    handleError (error) {
     if (error.response) {
    // The request was made and the server responded with a status code
    // that falls out of the range of 2xx
    switch (error.response.status) {
      case 400: {
        this.errorMessage = 'Both login name and password are required.'
        break
      }
      case 401: {
        this.errorMessage = 'Incorrect username or password'
        break
      }
      default: 
        this.errorMessage = 'Sorry. There was an error processing your request.'
    }
  } else if (error.request) {
    // The request was made but no response was received
    console.log(error.request)
  } else {
    // Something happened in setting up the request that triggered an Error
    console.log('Error', error.message)
  }
  console.log(error.config)
} 
  }
}
</script>

<style>
.button {
    margin-top: 0.5rem;
}

</style>