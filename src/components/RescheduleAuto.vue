<template>
  <div>
    <select v-model="selectedSession">
      <option v-for="session in sessions" :key="session.id" :value="session.id">
        {{ session.start_date_in_browser_time_zone }} - {{ session.test_center.city }} - {{ session.category.english_name }}
      </option>
    </select>
    <button @click="autoReschedule">Auto-Reschedule</button>
    <p v-if="message">{{ message }}</p>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      sessions: [],
      selectedSession: null,
      message: ''
    }
  },
  mounted() {
    this.fetchSessions()
  },
  methods: {
    async fetchSessions() {
      try {
        const res = await axios.get(`${import.meta.env.VUE_APP_API_URL}/individual_labor_space/exam_sessions?category_id=159`)
        this.sessions = res.data.data
        if(this.sessions.length > 0) this.selectedSession = this.sessions[0].id
      } catch(e) {
        this.message = 'Error fetching sessions'
      }
    },
    async autoReschedule() {
      try {
        if(!this.selectedSession) return
        const reservationId = 1054571  // your reservationId
        const res = await axios.post(`${import.meta.env.VUE_APP_API_URL}/individual_labor_space/temporary_seats`, {
          exam_session_id: this.selectedSession,
          reservation_id: reservationId
        })
        if(res.data) this.message = 'Reschedule applied successfully'
      } catch(e) {
        this.message = 'Error during rescheduling'
      }
    }
  }
}
</script>