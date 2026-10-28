# Week 03 — Answers

### 1. In EventList, what goes wrong if you use the array index as `:key` and the list gets filtered while a card has local state (for example an open details panel)?
If we use the array index as `:key`, Vue may reuse the wrong component when the list is filtered because the indexes can refer to different events. As a result, local state, such as an open details panel, may appear on the wrong event card. Using `:key="e.id"` gives each event a stable identity and helps Vue track the correct component.

### 2. Why is `visible` a computed and not a function called from the template? What is the difference when you type in the search box?
`visible` is a computed because it calculates the list of events based on the events, search text, and selected category. Vue automatically recalculates it when its reactive dependencies change and caches the result until those dependencies change. A function called from the template may run again during rendering, even when its result has not changed. When we type in the search box, `v-model` updates `search`, and `visible` updates automatically to display matching events.

### 3. EventCard emits `book` instead of changing `event.booked`. Give two reasons.
First, props are read-only, so `EventCard` should not directly modify the event data it receives from its parent. Second, `App.vue` owns the events and handles booking changes in `onBook()`, keeping the booking logic in one place and allowing the parent to check seat availability before updating the data.

### 4. You write `const { booked } = reactive(event); booked++`. The page does not change. Why?
Destructuring `booked` from a reactive object into a primitive variable disconnects that local variable from the original reactive property. Increasing `booked` changes the local variable rather than `event.booked`, so Vue does not detect a change to the original property. Instead, update `event.booked` directly or use `toRef()` to preserve the reactive connection.

### 5. Where does `myBookings` live, and why not in EventCard or EventList?
`myBookings` lives in `App.vue`, where it is declared as `ref<number[]>([])` and stores one event ID for every seat booked by the current user. It should not live in `EventCard` because that component displays one event and emits booking requests, or in `EventList` because that component filters and displays events. Keeping `myBookings` in `App.vue` allows the application to manage booking updates, calculate `bookingCount`, and save bookings to `localStorage` consistently.
