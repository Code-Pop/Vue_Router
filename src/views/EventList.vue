<template>
  <div class="events">
    <EventCard v-for="event in events" :key="event.id" :event="event" />

    <router-link
      :to="{ name: 'EventList', query: { page: page - 1 } }"
      rel="prev"
      v-if="page != 1"
      >Prev Page</router-link
    >

    <router-link
      :to="{ name: 'EventList', query: { page: page + 1 } }"
      rel="next"
      v-if="isNextPage"
      >Next Page</router-link
    >
  </div>
</template>

<script>
import EventCard from '@/components/EventCard.vue'
import EventService from '@/services/EventService.js'
import { watchEffect } from 'vue'

export default {
  name: 'EventList',
  components: {
    EventCard
  },
  data() {
    return {
      events: null,
      totalEvents: 0,
      perPage: 2
    }
  },
  created() {
    watchEffect(() => {
      this.events = null
      EventService.getEvents(this.perPage, this.page)
        .then(response => {
          this.events = response.data
          this.totalEvents = response.headers['x-total-count']
        })
        .catch(error => {
          console.log(error)
        })
    })
  },
  computed: {
    page() {
      return parseInt(this.$route.query.page) || 1
    },
    isNextPage() {
      var totalPages = Math.ceil(this.totalEvents / this.perPage)
      return this.page < totalPages
    }
  }
}
</script>

<style scoped>
.events {
  display: flex;
  flex-direction: column;
  align-items: center;
}
</style>
