# How to run
- cd vue-grove-scheduler
- npm install
- npm run dev
- Go to http://localhost:8080/
- Allow web notifications

# About
I use livestamp for the timers. When an event starts, I send a web notification and move the event to the past section and add the next event back.

# sources
- Used webpack boilerplate
- VueResource for http request
- Later.js for cron parsing
- Moment.js for "from now" formatting
- Livestamp.js for dynamic timers