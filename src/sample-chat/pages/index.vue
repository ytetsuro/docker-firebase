<template>
<div>
  <NavBar/>
  <section class="container">
    <div>
      <p>Room List</p>

      <button @click="createRoom">Create Room!</button><br>

      <div v-if="rooms">
        <div v-for="room in rooms" :key="room['key']">
         {{ room.create_user_id }}
       </div>
      </div>

      <!--
      <app-logo/>
      <h1 class="title">
        sample-chat
      </h1>
      <h2 class="subtitle">
        Nuxt.js project
      </h2>
      <div class="links">
        <a
          href="https://nuxtjs.org/"
          target="_blank"
          class="button--green">Documentation</a>
        <a
          href="https://github.com/nuxt/nuxt.js"
          target="_blank"
          class="button--grey">GitHub</a>
      </div>
      -->

    </div>
  </section>
</div>
</template>

<script>
//import AppLogo from '~/components/AppLogo.vue'
import NavBar from '~/components/NavBar.vue'
import firebase from '~/plugins/firebase'

const database = firebase.database();
const roomsRef = database.ref('/rooms');

export default {
  data(){
      rooms: null
  },
  components: {
    NavBar
  },
  methods:{
    createRoom: function() {

      var user = firebase.auth().currentUser
      console.log(user)

      roomsRef.push({
        create_user_id: user.uid,
        create_user_name: user.displayName,
        create_at: new Date().getTime()
      }).then(function (res) {
          console.log(res);
      })
    },
  },
  mounted: function(){
    const self = this
    self.rooms = []

    roomsRef.once('value').then(function(snapshot) {
      //console.log(snapshot.val())

      var tmp_rooms = []
      snapshot.forEach(function(data) {
        console.log(data.key)
        console.log(data.val())

        tmp_rooms.key = data.key
        tmp_rooms.create_user_id = data.val().create_user_id
        self.rooms.push(tmp_rooms)
      });

      //console.log(self.rooms)
      
    })
  }

}
</script>

<style>
.container {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.title {
  font-family: "Quicksand", "Source Sans Pro", -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif; /* 1 */
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
}

.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.links {
  padding-top: 15px;
}
</style>
