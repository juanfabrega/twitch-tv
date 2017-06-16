<template>
  <div id="app">

    <!--top nav bar-->
    <md-whiteframe class="md-fixed">
      <md-toolbar id="top-nav" class="md-accent md-fixed">
        <md-icon id="camera-icon">videocam</md-icon>
        <img id="header-logo" src="./assets/twitch-logo-large.png" alt="Twitch Logo">
        <md-button class="md-icon-button">
          <img src="./assets/github-logo.svg">
        </md-button>
      </md-toolbar>
    </md-whiteframe>

    <div id="container">
      <!--cards with streamer information, generated via vue directives-->
      <md-layout :md-gutter="16">
        <md-layout md-flex-xsmall="100" md-flex-medium="50" v-for="streamer in streamers" :key="streamer.key">
          <md-card md-with-hover>
            <md-card-header>
              <div>
                <img id="profile-img" src="./assets/twitch-logo.png">
                <div id="head-subhead">
                <h3 class="md-title">{{ streamer.channel.channelName }}</h3>
                <p class="md-subhead">Subtitle here</p>
                </div>
              </div>
            </md-card-header>
            <md-card-content>
              {{ streamer.channel.bio }}
            </md-card-content>

            <md-card-actions>
              <md-button class="not-streaming" v-bind:class="{ streaming : streamer.currentStream.active }"><md-icon>videocam</md-icon></md-button>
            </md-card-actions>
          </md-card>
        </md-layout>
      </md-layout>
      <!--end of cards-->
    </div>
  </div>
</template>

<script>

export default {
  name: 'app',
  //mounted: function() {
    //this.getChannelInfo()
  //},
  data () {
    return {
      streamers: [
        {
          key: '1',
          channel: {
            channelName: 'channel1',
            bio: '',
            profileImgUrl: '',
            bannerImgUrl: '',
            followers: 0,
            channelUrl: ''
          },
          currentStream: {
            active: false,
            streamingGame: '',
            viewers: 0,
            previewImgUrl: ''
          }
        },
        {
          key: '2',
          channel: {
            channelName: 'channel2',
            bio: '',
            profileImgUrl: '',
            bannerImgUrl: '',
            followers: 0,
            channelUrl: ''
          },
          currentStream: {
            active: false,
            streamingGame: '',
            viewers: 0,
            previewImgUrl: ''
          }
        },
        {
          key: '3',
          channel: {
            channelName: 'channel3',
            bio: '',
            profileImgUrl: '',
            bannerImgUrl: '',
            followers: 0,
            channelUrl: ''
          },
          currentStream: {
            active: false,
            streamingGame: '',
            viewers: 0,
            previewImgUrl: ''
          }
        }
      ],
      users: [
        "ESL_SC2",
        "OgamingSC2",
        "cretetion",
        "freecodecamp",
        "storbeck",
        "habathcx",
        "RobotCaleb",
        "noobs2ninjas"]
    }
  },
  methods: {
    getChannelInfo: function() {
      // GET /someUrl
      this.$http.get('https://api.twitch.tv/kraken/channels/freecodecamp?client_id=94zaltt5of3jzoq8zeofdz0bxt26tk').then(response => {
        console.log(response);
        // get body data
        this.streamers[0].channel.channelName = response.body.display_name;

      }, response => {
        // error callback
      });
    }
  }
}
</script>

<style>
  body {
    background: linear-gradient(60deg, #673AB7, #512DA8);
  }

  .md-title {
    display: inline;
  }

  #header-logo {
    height: 35px;
    width: auto;
    margin: 0 auto;
  }
  #head-subhead {
    display: inline-block;
  }

  .md-card {
    margin-bottom: 16px;
  }

  #profile-img {
    width: 50px;
    height: 50px;
    object-fit: cover;
    border-radius: 50%;
    margin-right: 10px;
    vertical-align: top;
  }

  #container {
    padding: 16px;
  }

  #camera-icon {
    margin: 0;
    margin-left: 10px;
    color: #6441a5;
  }

  #top-nav {
    margin-bottom: 20px;
    background-color: #fff;
  }

  .not-streaming {
    color: red;
  }

  .not-streaming:before {
    content: 'OFFLINE';
    vertical-align: middle;
    padding-right: 3px;
  }

  .streaming {
    color: green;
  }

  .streaming:before {
    content: 'NOW STREAMING';
    vertical-align: middle;
  }
</style>
