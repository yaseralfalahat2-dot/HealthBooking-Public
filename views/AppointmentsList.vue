<template>
  <div>
    <nav class="navbar navbar-light bg-white shadow-sm mb-4">
      <div class="container d-flex justify-content-between align-items-center">
        <span class="fw-bold fs-5">Doctor Appointments</span>
        <router-link to="/" class="btn btn-outline-primary">⇆ Switch Page</router-link>
      </div>
    </nav>
    <div class="container mt-4">
      <h2 class="text-center mb-4 text-primary">All Appointments</h2>
      <div v-if="appointments.length === 0" class="text-center text-muted">
        No appointments found.
      </div>
      <div v-for="appt in appointments" :key="appt.appointmentId" class="card shadow mb-3 p-3">
        <div class="d-flex justify-content-between align-items-center">
          <div>
            <h5 class="mb-1">{{ appt.patientName }}</h5>
            <small class="text-muted">Slot: {{ appt.slot }} | Symptoms: {{ appt.symptoms }}</small><br/>
            <small class="text-muted">Created: {{ appt.createdAt }}</small>
          </div>
          <div>
            <select v-model="appt.status" @change="updateStatus(appt)" class="form-select form-select-sm" style="width: 150px;">
              <option value="Pending">Pending</option>
              <option value="In Progress">In Progress</option>
              <option value="Completed">Completed</option>
            </select>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
const API_BASE = "https://u82hg3kqak.execute-api.us-east-1.amazonaws.com/prod";

export default {
  name: "AppointmentsList",
  data() {
    return {
      appointments: []
    };
  },
  mounted() {
    fetch(`${API_BASE}/appointments`)
      .then(res => res.json())
      .then(data => {
        let body = data.body ? JSON.parse(data.body) : data;
        this.appointments = body.appointments || [];
      })
      .catch(err => {
        console.error("Error fetching appointments:", err);
      });
  },
  methods: {
    updateStatus(appt) {
      fetch(`${API_BASE}/appointments/${appt.appointmentId}`, {
        method: "PATCH",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ status: appt.status })
      })
        .then(() => alert("Status updated to: " + appt.status))
        .catch(err => console.error("Error updating status:", err));
    }
  }
};
</script>
