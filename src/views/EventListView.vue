<script setup>
import EventCard from '@/components/EventCard.vue'
import EventService from '@/services/EventService.js'
import { defineProps, onMounted, ref, watchEffect } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()

const events = ref(null)
let totalEvents = 0
const props = defineProps({
  page: {
    type: Number,
    required: true,
  },
})
onMounted(() => {
  watchEffect(() => {
    events.value = null
    EventService.getEvents(2, props.page)
      .then((response) => {
        events.value = response.data
        totalEvents = response.headers['x-total-count']
      })
      .catch((error) => {
        router.push({ name: 'networkerror' })
      })
  })
})

function hasNextPage() {
  let totalPage = Math.ceil(totalEvents / 2)
  return props.page < totalPage
}
</script>

<template>
  <h1>Events For Good</h1>
  <div class="events">
    <EventCard v-for="event in events" :key="event.id" :event="event" />

    <div class="pagination">
      <router-link
        id="page-prev"
        :to="{ name: 'event-list', query: { page: page - 1 } }"
        rel="prev"
        v-if="page != 1"
        >&#60; Previous</router-link
      >

      <router-link
        id="page-next"
        :to="{ name: 'event-list', query: { page: page + 1 } }"
        rel="next"
        v-if="hasNextPage()"
        >Next &#62;</router-link
      >
    </div>
  </div>
</template>

<style scoped>
.events {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.pagination {
  display: flex;
  width: 290px;
}
.pagination a {
  flex: 1;
  text-decoration: none;
  color: #2c3e50;
}

#page-prev {
  text-align: left;
}

#page-next {
  text-align: right;
}
</style>
