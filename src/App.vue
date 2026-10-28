```vue
<script setup lang="ts">
import { computed, ref, watch } from "vue";
import EventList from "./components/EventList.vue";
import { initialEvents } from "./data/events";
import type { CampusEvent } from "./types";

// A reactive copy of the hard-coded events
const events = ref<CampusEvent[]>(
  initialEvents.map(e => ({ ...e }))
);

// TODO 1: Store one event ID for every seat booked
const myBookings = ref<number[]>([]);

// Restore bookings saved in localStorage
try {
  const saved = localStorage.getItem("myBookings");

  if (saved) {
    const savedIds: unknown = JSON.parse(saved);

    if (Array.isArray(savedIds)) {
      for (const id of savedIds) {
        if (typeof id !== "number") continue;

        const event = events.value.find(e => e.id === id);

        // Restore only valid bookings that fit the event capacity
        if (event && event.booked < event.capacity) {
          event.booked++;
          myBookings.value.push(id);
        }
      }
    }
  }
} catch {
  // Ignore invalid or unavailable saved data
}

// TODO 2: Count all seats booked by the current user
const bookingCount = computed(() => myBookings.value.length);

// TODO 3: Book a seat if the event is not full
function onBook(id: number) {
  const event = events.value.find(e => e.id === id);

  if (!event || event.booked >= event.capacity) {
    return;
  }

  event.booked++;
  myBookings.value.push(id);
}

// TODO 4: Save bookings whenever the list changes
watch(
  myBookings,
  bookings => {
    try {
      localStorage.setItem("myBookings", JSON.stringify(bookings));
    } catch {
      // The app can still work if storage is unavailable
    }
  },
  { deep: true }
);
</script>

<template>
  <main>
    <header>
      <h1>Campus Events</h1>

      <p class="mine">
        You have booked {{ bookingCount }}
        {{ bookingCount === 1 ? "seat" : "seats" }}.
      </p>
    </header>

    <EventList :events="events" @book="onBook" />
  </main>
</template>

<style scoped>
header {
  margin-bottom: 16px;
}

.mine {
  color: #6b7280;
  margin: 0;
}
</style>
```