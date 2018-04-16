<template>
  <div class="event-card" v-bind:class="{ past: isPassed }">
    <h1>{{name}}</h1>
    <span class="date"></span>
  </div>
</template>

<script>
import $ from 'jquery'
import moment from 'moment'
import livestamp from '@bassettsj/livestamp/livestamp.js'

export default {
  name: 'Event',
  props: ['isPassed', 'cron', 'name'],
  data: {
    timeout: null,
    date: '',
    alertSent: false
  },
  created: function () {
    this.$nextTick(function () {
      var $this = this;
      var schedule = later.schedule(later.parse.cron(this.cron));
      var options = { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric', hour: 'numeric'};

      this.date = schedule.next(1);
      if (this.isPassed) {
        this.alertSent = true; // Don't send notification for past event
        this.date = schedule.prev(1);
      }

      // Listener for when event happens
      $(this.$el).find('.date').livestamp(this.date).on('change.livestamp', (event, from, to) => {
        if (!this.alertSent && (this.date - new Date() < 0)) {
          this.alertSent = true;
          this.$parent.$emit('markAsDone', $this);
        }
      });
    })
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.event-card {
  background-color: #fff;
  box-shadow: 0 2px 4px 0 rgba(0,0,0,.2);
  padding: 16px;
  margin-bottom: 20px;
  transition: opacity .3s;
}

.event-card.past {
  opacity: 0.5;
}

.event-card.past h1 {
  text-decoration: line-through;
}

h1 {
  color: #4e5f6f;
}
</style>
