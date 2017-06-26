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
        <!--TODO Need to figure out how TF to structure this IF statement to prevent async load errors-->
        <md-layout md-flex-xsmall="100" md-flex-medium="50" md-flex-large="25" v-for="streamer in streamers" :key="streamer.key">
          <md-card id="card" md-with-hover v-bind:href="streamer.channel.channelUrl" target="_blank">
            <md-card-header>
              <div>
                <img v-if="streamer.channel.profileImgUrl" id="profile-img" v-bind:src=streamer.channel.profileImgUrl>
                <img v-else id="profile-img" src="./assets/twitch-logo.png">
                <div id="head-subhead">
                  <h3 class="md-title">{{ streamer.channel.channelName }}</h3>
                  <p class="md-subhead"><em>{{ streamer.channel.followers }} Followers</em></p>
                </div>
              </div>
            </md-card-header>
            <md-card-content>
              {{ streamer.channel.bio }}
            </md-card-content>
            <md-card-content id="stream-status">
              <h3 class="not-streaming" v-bind:class="{ streaming : streamer.stream.active }"><md-icon>videocam</md-icon></h3>
            </md-card-content>
            <md-card-content v-if="streamer.stream.active" id="active-stream">
              <div>
                <img id="stream-img" v-bind:src=streamer.stream.previewImgUrl>
                <div class="stream-info">
                  <p class="stream-right-text"><md-icon>videogame_asset</md-icon> {{ streamer.stream.streamingGame }}</p>
                  <p class="stream-right-text"><md-icon>people</md-icon> {{ streamer.stream.viewers }}</p>
                </div>
              </div>
            </md-card-content>
            <div id="invisible"></div>
            <div id="channel-link">
              <h4 class="channel-link-txt"><a id="anchor-override" v-bind:href="streamer.channel.channelUrl" target="_blank">GO TO CHANNEL <md-icon>chevron_right</md-icon></a></h4>
            </div>
          </md-card>
        </md-layout>
      </md-layout>
      <!--end of cards-->
    </div>
  </div>
</template>

<script>
import Vue from 'vue'

export default {
  name: 'app',
  mounted: function() {
    this.getChannelInfo();
    this.getStreamInfo();
  },
  data () {
    return {
      streamers: [],
      users: [
        "ESL_SC2",
        "OgamingSC2",
        "cretetion",
        "freecodecamp",
        "storbeck",
        "habathcx",
        "RobotCaleb",
        "ninja",
        "garenatw",
        "comster404"
      ],
      default_img: '.assets/twitch-logo-large.png'
    }
  },
  methods: {
    getChannelInfo: function() {
      for (let i = 0; i < this.users.length; i++) {
        this.$http.get('https://api.twitch.tv/kraken/channels/' + this.users[i] + '?client_id=94zaltt5of3jzoq8zeofdz0bxt26tk').then(response => {
          console.log(response);

          Vue.set(this.streamers, i,
            {
              key: response.body._id,
              channel: {
                channelName: response.body.display_name,
                bio: response.body.status,
                profileImgUrl: response.body.logo,
                followers: response.body.followers,
                channelUrl: response.body.url
              },
              stream: {
                active: false
              }
            });
        },
        response => {
          console.log('Could not reach API or account does not exist');
          Vue.set(this.streamers, i,
            {
              key: response.body.status + i,
              channel: {
                channelName: this.users[i],
                bio: 'Channel does not exist',
                profileImgUrl: null,
                followers: 0,
                channelUrl: 'http://www.twitch.tv'
              },
              stream: {
                active: false
              }
            });
        });
      }
    },
    getStreamInfo: function() {
      for (let i = 0; i < this.users.length; i++) {
        this.$http.get('https://api.twitch.tv/kraken/streams/' + this.users[i] + '?client_id=94zaltt5of3jzoq8zeofdz0bxt26tk').then(response => {
          if (response.body.stream != null) {
            Vue.set(this.streamers[i], 'stream',
              {
                active: true,
                streamingGame: response.body.stream.game,
                viewers: response.body.stream.viewers,
                previewImgUrl: response.body.stream.preview.large
              });
          }
          else {
            Vue.set(this.streamers[i], 'stream',
              {
                active: false,
                streamingGame: '',
                viewers: 0,
                previewImgUrl: ''
              });
          }
          console.log(response);
        },
        response => {
          console.log('Could not reach API or account does not exist');
        });
      }
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

  #card {
    position: relative;
  }

  .md-card {
    margin-bottom: 16px;
    width: 100%;
  }

  .md-card-actions {
    padding: 16px;
  }

  #profile-img {
    width: 50px;
    height: 50px;
    object-fit: cover;
    border-radius: 50%;
    margin-right: 10px;
    vertical-align: top;
  }

  #active-stream {
    padding: 0 16px 16px 16px;
  }

  #stream-img {
    width: 50%;
    height: auto;
    object-fit: cover;
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

  #stream-status {
    border-top: 1px solid #ccc;
    padding: 0 16px;
  }

  #anchor-override {
    color: #6441A4;
  }

  #anchor-override:hover {
    text-decoration: none;
  }

  #channel-link {
    border-top: 1px solid #ccc;
    position: absolute;
    width: 100%;
    bottom: 0;
  }

  #invisible {
    width: 100%;
    height: 62px;
  }

  .channel-link-txt {
    text-align: center;
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

  .stream-img-container {
    display: inline-block;
  }

  .stream-info {
    display: inline-block;
  }

  .stream-right-text {
    display: block;
  }
</style>
