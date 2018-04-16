<template>
  <div class="scheduler">
    <h1>Grove Scheduler</h1>
    <event
      v-for="event in prevEvents"
      v-bind:isPassed="true"
      v-bind:cron="event.attributes.cron"
      v-bind:name="event.attributes.name"
    ></event>
    <div v-if="prevEvents.length > 0" class="now-line"></div>
    <event
      v-for="event in nextEvents"
      v-bind:isPassed="false"
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
      prevEvents: [],
      nextEvents: []
    }
  },
  created: function() {
    this.$http.get('https://scheduler-challenge.herokuapp.com/schedule').then(response => {
      // success callback
      var events = response.body.data;

      for (var i = 0; i < events.length; i++) {
        var schedule = later.schedule(later.parse.cron(events[i].attributes.cron));

        // 3 hours in milliseconds
        if (schedule.prev(1) - new Date() >= -10800000) {
          this.prevEvents.push(events[i]);
        }
      }

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

      this.nextEvents = events;
    }, response => {
      // error callback
      console.log(response);
    });

    // Listener
    this.$on('markAsDone', (event) => {
      console.log("MARKED AS DONE", event);
      this.notifyEvent(event.name);
      var indexToBeMarked = this.nextEvents.findIndex((el) => {
        return el.attributes.name === event.name;
      });
      console.log("SPLICE", indexToBeMarked);
      var newEvent = {
        attributes: {
          cron: event.cron,
          name: event.name
        }
      }
      this.nextEvents[indexToBeMarked] = newEvent;
      this.prevEvents.push(newEvent);
    });
  },
  methods: {
    notifyEvent: function(name) {
      new Notification(name);
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.now-line {
  position: relative;
  height: 2px;
  margin: 40px 0;
  background-color: red;
}

.now-line::before {
  content: 'Now';
  display: inline-block;
  color: #fff;
  height: 24px;
  line-height: 24px;
  background-color: red;
  border-radius: 24px;
  padding: 2px 16px;

  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate3d(-50%, -50%, 0);
}

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
