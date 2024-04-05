<template>
  <q-page style="display: flex; justify-content: center; flex-direction: column">
    <div class="login-form">
      <q-form @submit.prevent="login" class="login-form-q">
        <q-input outlined v-model="username" label="Username" />
        <q-input outlined v-model="password" label="Password" type="password" style="margin-top: 5px;" />
        <q-btn type="submit" label="Login" color="primary" class="login-btn" />
      </q-form>
    </div>
  </q-page>
</template>

<script>
export default {
  data () {
    return {
      username: '',
      password: ''
    }
  },
  methods: {
    login () {
      const requestData = {
        username: this.username,
        password: this.password
      }

      fetch('http://hr.test/login.php', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(requestData)
      })
        .then(response => {
          if (response.ok) {
            localStorage.setItem('authenticated', 'true') // very simple one, normally it should be token with session or vuex mechanism
            return response.json()
          } else {
            throw new Error('Invalid username or password')
          }
        })
        .then(data => {
          console.log(data.message)
          this.$router.push('/admin')
        })
        .catch(error => {
          console.error('Error:', error)
          alert(error.message)
        })
    }
  }
}
</script>
