<template>
  <div class="event-card">
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
  props: ['type', 'cron', 'name'],
  data: {
    timeout: null,
    next: '',
    fromNow: 0,
    alertSent: false
  },
  created: function () {
    this.$nextTick(function () {
      var $this = this;
      var schedule = later.schedule(later.parse.cron(this.cron));
      var options = { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric', hour: 'numeric'};

      this.next = schedule.next(1);
      this.fromNow = this.next - new Date();

      $(this.$el).find('.date').livestamp(this.next).on('change.livestamp', function(event, from, to) {
        if (!$this.alertSent && ($this.next - new Date() < 0)) {
          $this.alertSent = true;
          $this.notifyEvent($this.name, $this.next);
        }
      });
    })
  },
  methods: {
    notifyEvent: function(title, date, delay) {
      new Notification(title, null, date);
    }
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
}
</style>
