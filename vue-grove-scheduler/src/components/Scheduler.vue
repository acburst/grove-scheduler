<template>
  <div class="scheduler">
    <h1>{{ msg }}</h1>
    <event
      v-for="event in events"
      v-bind:key="event.id"
      v-bind:type="event.type"
      v-bind:cron="event.attributes.cron"
      v-bind:name="event.attributes.name"
    ></event>
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
      var events = response.body.data;
      events.sort(function(a,b) {
        var scheduleA = later.schedule(later.parse.cron(a.attributes.cron));
        var scheduleB = later.schedule(later.parse.cron(b.attributes.cron));
        if (scheduleA.next(1) < scheduleB.next(1)) {
          return -1;
        }
        if (scheduleA.next(1) > scheduleB.next(1)) {
          return 1;
        }
        return 0
      });

      this.events = events;
    }, response => {
      // error callback
      console.log(response);
    });
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.scheduler {
  width: 40%;
  margin: 0 auto;
}
@media screen and (max-width: 600px) {
  .scheduler {
    width: 100%;
    padding: 8px;
    box-sizing: border-box;
  }
}
</style>
