<template>
  <div class="container my-3">
    <div>
      <div class="d-flex justify-content-center">
        <h1>Outlook Calendar Events</h1>
      </div>
      <div class="d-flex justify-content-center">
        <button @click="redirectToMicrosoftLogin" class="btn btn-light border">
          Login with Microsoft
        </button>
      </div>

      <div v-if="events.length">
        <h2>Your Events:</h2>
        <ul>
          <li v-for="event in events" :key="event.id">
            <strong>{{ event.subject }}</strong>
            <br />
            Start: {{ event.start.dateTime }}
            <br />
            End: {{ event.end.dateTime }}
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      events: [],
    };
  },
  methods: {
    redirectToMicrosoftLogin() {
      const clientId = "YOUR_CLIENT_ID";
      const tenant = "common"; // Supports both work and personal accounts
      const redirectUri = `${window.location.origin}/callback`;
      const scope = "Calendars.Read";
      const state = "random_state";
      const authUrl = `https://login.microsoftonline.com/${tenant}/oauth2/v2.0/authorize?client_id=${clientId}&response_type=token&redirect_uri=${redirectUri}&scope=${scope}&state=${state}`;
      window.location.href = authUrl;
    },
    async fetchEvents() {
      const accessToken = localStorage.getItem("outlookAccessToken");

      if (!accessToken) {
        console.error("No access token found");
        return;
      }

      try {
        const response = await axios.get("https://graph.microsoft.com/v1.0/me/events", {
          headers: {
            Authorization: `Bearer ${accessToken}`,
          },
        });
        this.events = response.data.value;
        console.log("Events fetched successfully:", this.events);
      } catch (error) {
        console.error("Error fetching events:", error.response || error.message);
      }
    },
  },
  mounted() {
    this.fetchEvents();
  },
};
</script>
