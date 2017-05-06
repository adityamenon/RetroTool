<template>
  <div id="app">
    <router-view></router-view>
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
    this.auth0 = new auth0.WebAuth({
      domain: 'retrotool.auth0.com',
      clientID: 'TUp4FQ7ycV7UYcLahoa-FGGjlgVwVVXQ',
      callbackURL: 'http://localhost:3000/callback'
    })

    this.auth0.parseHash(window.location.hash, (err, result) => {
      parent.postMessage(err || result, 'http://localhost:8080/')
      if (result) {
        this.auth0.client.userInfo(result.accessToken, (err, user) => {
          if (err) {
            console.log(err)
          } else {
            localStorage.setItem('userDetails', JSON.stringify(user))
          }

          window.close()
        })
      }
    })
  }
}
</script>

<style>
    html, body{
        font-family: 'Ubuntu', sans-serif;
      background-color: #1e434c;
    }
    #app {
      -webkit-font-smoothing: antialiased;
      -moz-osx-font-smoothing: grayscale;
      color: #2c3e50;
    }
</style>
