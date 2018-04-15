<template>
  <div class="event-card">
    <h1>{{name}}</h1>
    <span class="date">{{formattedDate}}</span>
  </div>
</template>

<script>
import moment from 'moment'
export default {
  name: 'Event',
  props: ['type', 'cron', 'name'],
  computed: {
    formattedDate: function() {
      var schedule = later.schedule(later.parse.cron(this.cron));

      var options = { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric', hour: 'numeric'};
      console.log(later.parse.cron(this.cron), (new Date()).toLocaleDateString(options), moment(schedule.next(1)));

      return moment(schedule.next(1)).fromNow();
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
