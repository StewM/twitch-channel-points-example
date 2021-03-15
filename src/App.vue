<template>
  <div id="app">
    <a v-if="!access_token" :href="login_url">Login to Twitch</a>
    
    <ChannelPoints v-if="access_token" :access_token="access_token" />
  </div>
</template>

<script>
import ChannelPoints from './components/ChannelPoints.vue'

export default {
  name: 'App',
  components: {
    ChannelPoints
  },
  data: function () { 
    return {
      access_token: ''
    }
  },
  computed: {
    login_url: function () {
      let url = `https://id.twitch.tv/oauth2/authorize?client_id=${process.env.VUE_APP_TWITCH_CLIENT_ID}&redirect_uri=http://localhost:8080&response_type=token&scope=channel:read:redemptions`
      return url
    }
  },
  mounted: function () {
    let params = parseParms(document.location.hash.substring(1))

    if(params.access_token){
      this.access_token = params.access_token
    }
  }
}

function parseParms(str) 
{
 var pieces = str.split("&"), data = {}, i, parts;
    // process each query pair
    for (i = 0; i < pieces.length; i++) {
        parts = pieces[i].split("=");
        if (parts.length < 2) {
            parts.push("");
        }
        data[decodeURIComponent(parts[0])] = decodeURIComponent(parts[1]);
    }
    return data;
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
