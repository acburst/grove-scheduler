<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <event v-for="event in events"></event>
  </div>
</template>

<script>
import Event from './Event.vue'
export default {
  name: 'Scheduler',
  components: {Event},
  data () {
    return {
      msg: 'Welcome to Your Vue.js App',
      events: {}
    }
  },
  created: function() {
    this.$http.get('https://scheduler-challenge.herokuapp.com/schedule').then(response => {
      // success callback
      console.log(response);
      this.events = response.body.data;
    }, response => {
      // error callback
      console.log(response);
    });
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
