<template>
  <div id="app">
    <router-view :ds="ds"></router-view>
  </div>
</template>

<script>
import * as auth0 from 'auth0-js'
export default {
  name: 'app',
  data: {
    userDetails: {},
    auth0: new auth0.WebAuth({
      domain: 'retrotool.auth0.com',
      clientID: 'TUp4FQ7ycV7UYcLahoa-FGGjlgVwVVXQ',
      callbackURL: 'http://localhost:3000/callback'
    })
  },
  created: function () {
//    alert('hey')
    this.auth0 = new auth0.WebAuth({
      domain: 'retrotool.auth0.com',
      clientID: 'TUp4FQ7ycV7UYcLahoa-FGGjlgVwVVXQ',
      callbackURL: 'http://localhost:3000/callback'
    })

    this.auth0.parseHash(window.location.hash, (err, result) => {
      console.log('hey', result, err)
      parent.postMessage(err || result, 'http://localhost:8080/')
      this.auth0.client.userInfo(result.accessToken, (err, user) => {
        console.log(err)
        localStorage.setItem('userDetails', JSON.stringify(user))
        window.close()
      })
    })
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
</style>
