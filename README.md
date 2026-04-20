# SkyNow
 
A real-time weather dashboard built for **Chapter 4 — Lab Exercise 3** of the Cross Platform Application Development module.
 
Fetches live weather data using both the native **Fetch API** (with `async/await`) and **jQuery AJAX**, demonstrating how two different approaches can work side-by-side in the same application.
 
---
 
# Features
 
- **City search** — search any city in the world by name
- **Current weather card** — shows temperature, weather description, humidity, wind speed, and local time
- **7-day forecast** — a row of cards showing daily high/low temperatures and weather icons
- **Skeleton loading animation** — pulsing grey placeholders appear while data is being fetched
- **Error handling** — friendly error messages for network failures, HTTP errors, and timeouts, with a retry button
- **Input validation** — search requires at least 2 characters before any API call is made
- **Debounced search** — typing quickly does not spam the API; requests are delayed by 500ms
- **Request timeout** — any API call that takes longer than 10 seconds is automatically cancelled
- **°C / °F toggle** — switch temperature units instantly without making a new API call
- **Recent searches** — the last 5 searched cities are saved and displayed as clickable chips
---
 
# APIs Used
 
| API | Purpose | Method Used |
|-----|---------|-------------|
| [Open-Meteo Geocoding](https://open-meteo.com/en/docs/geocoding-api) | Converts a city name into latitude & longitude | Fetch API |
| [Open-Meteo Forecast](https://open-meteo.com/en/docs) | Retrieves current weather and 7-day forecast | Fetch API |
| [WorldTimeAPI](https://worldtimeapi.org/) | Gets the local time for the searched city | jQuery `$.getJSON()` |
 
All three APIs are free and require **no authentication key**.
 
---
 
# Project Structure
 
```
WeatherNow/
├── index.html   — Page structure, skeleton placeholders, jQuery CDN import
├── style.css    — All styling including the shimmer animation
└── app.js       — All JavaScript logic (Fetch API, jQuery, debounce, etc.)
```
 
No build tools, no frameworks, no install steps — just open `index.html` in a browser.
 
---
 
# How to Run
 
1. Download or clone this folder
2. Open `index.html` in any modern browser (Chrome, Firefox, Edge)
3. Type a city name and press **Search** or hit **Enter**
> No local server is required. All APIs support cross-origin requests (CORS).
 
---
 
# Task Coverage
 
This project fulfils all four tasks from the lab spec:
 
### Task 1 — HTML & CSS: Dashboard Shell with Skeleton Loaders
- Search bar with text input and Search button
- Current weather card with all six required fields
- 7-day forecast row with 7 cards (day, icon, high, low)
- Skeleton shimmer animation using `@keyframes shimmer` exactly as specified

### Task 2 — Fetch API: Geocoding and Weather Data
- Geocoding API called first to resolve city name → lat/lon
- Weather API called second using those coordinates
- Empty results handled with a UI error message — no `throw`
- `try/catch` wraps all fetch calls for network error handling
- Weathercode integers converted to text and emoji via a lookup object
- Skeletons removed and UI populated on success
### Task 3 — jQuery AJAX: Local Time Integration
- `$.getJSON()` fetches the WorldTimeAPI after weather loads
- `.done()`, `.fail()`, `.always()` chained — no callbacks inside `$.ajax()`
- Falls back to the browser's local time if the API fails
- `.always()` logs a completion timestamp to the browser console
### Task 4 — Error Handling & Edge Cases
- HTTP errors detected via `response.ok` and thrown with the status code
- Validation rejects inputs shorter than 2 characters before any API call
- Debounce at 500ms prevents redundant calls during fast typing
- `AbortController` cancels any request that exceeds 10 seconds
### Bonus
- **Recent searches** — last 5 cities stored in `localStorage` as clickable chips
- **°C / °F toggle** — conversion done in JavaScript; no new API call needed
---
 
# Key Concepts Demonstrated
 
**Fetch API with `async/await`**
```js
const response = await fetch(url, { signal: controller.signal });
if (!response.ok) throw new Error(`HTTP ${response.status}`);
const data = await response.json();
```
 
**jQuery AJAX chaining**
```js
$.getJSON(url)
  .done(data  => { /* update UI */ })
  .fail(()    => { /* use browser time as fallback */ })
  .always(()  => { console.log('Completed at:', new Date().toISOString()); });
```
 
**Debounce**
```js
function debounce(fn, delay) {
  let timerId;
  return function(...args) {
    clearTimeout(timerId);
    timerId = setTimeout(() => fn.apply(this, args), delay);
  };
}
```
 
**AbortController timeout**
```js
const controller = new AbortController();
const timeoutId  = setTimeout(() => controller.abort(), 10000);
const response   = await fetch(url, { signal: controller.signal });
clearTimeout(timeoutId);
```
 
---
 
# Reflection: Fetch API vs jQuery AJAX
 
Both approaches were used in this project — Fetch API for the main weather data chain and jQuery's `$.getJSON()` for the local time request. Here is a comparison:
 
| | Fetch API | jQuery AJAX |
|---|---|---|
| **Syntax** | Verbose but explicit — you manually check `response.ok` and call `.json()` | Concise — `$.getJSON()` parses JSON automatically |
| **Promise style** | `async/await` reads like synchronous code | `.done()/.fail()/.always()` chaining is readable but older-style |
| **Error handling** | HTTP errors (4xx, 5xx) do NOT automatically reject — you must check `response.ok` yourself | Same behaviour — HTTP errors go to `.fail()` only for network failures |
| **Browser support** | All modern browsers natively | Requires jQuery to be loaded |
| **Bundle size** | Zero — built into the browser | Adds ~30KB (jQuery) even if you only use AJAX |
 
**Personal preference:** The Fetch API with `async/await` is cleaner for chained dependent calls (geocoding → weather), where the result of one call feeds directly into the next. jQuery's `.done()/.fail()/.always()` pattern is very readable for a single independent request like the time lookup, but `async/await` scales better as complexity grows.
 
---
 
## Author
 
Year 2 Software Engineering student — Faculty of Computing
Cross Platform Application Development 
 