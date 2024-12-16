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
            <strong>{{ event.subject }}</strong
            ><br />
            Start: {{ event.start.dateTime }}<br />
            End: {{ event.end.dateTime }}
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import { fetchOutlookEvents } from "@/utils/outlook";

export default {
  name: "DisplayEvents",
  data() {
    return {
      events: [],
    };
  },
  methods: {
    redirectToMicrosoftLogin() {
      const clientId = "1c0e40a8-4c6d-458e-9ed6-2167e7f2f47e";
      const redirectUri = window.location.origin + "/callback";
      const scope = "Calendars.Read";
      const state = "random_state"; // Optional but recommended for security

      // const authUrl = `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=${clientId}&response_type=token&redirect_uri=${redirectUri}&scope=${scope}&state=${state}`;
      const authUrl = `https://login.microsoftonline.com/common/oauth2/v2.0/authorize`;
      window.location.href = authUrl;
    },
    async fetchEvents(accessToken) {
      try {
        this.events = await fetchOutlookEvents(accessToken);
      } catch (error) {
        console.error("Error fetching events:", error);
      }
    },
  },
  mounted() {
    const hashParams = new URLSearchParams(window.location.hash.slice(1));
    const accessToken = hashParams.get("access_token");

    if (accessToken) {
      this.fetchEvents(accessToken);
    }
  },
};
</script>
