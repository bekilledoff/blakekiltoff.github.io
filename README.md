<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Comedy Open Mics</title>
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; }

    body {
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
      background: #f7f8fa;
      color: #1a1a1a;
      height: 100vh;
      display: flex;
      flex-direction: column;
      overflow: hidden;
    }

    /* ── Header ── */
    header {
      background: #fff;
      border-bottom: 1px solid #e5e7eb;
      padding: 13px 20px;
      display: flex;
      align-items: center;
      gap: 12px;
      flex-shrink: 0;
      box-shadow: 0 1px 3px rgba(0,0,0,0.06);
    }
    header h1 {
      font-size: 1.1rem;
      font-weight: 700;
      letter-spacing: -0.3px;
      color: #111;
    }
    header .mic-icon { font-size: 1.3rem; }
    .event-count {
      margin-left: auto;
      font-size: 0.78rem;
      color: #9ca3af;
      white-space: nowrap;
    }

    /* ── Filter Bar ── */
    .filter-bar {
      background: #fff;
      border-bottom: 1px solid #e5e7eb;
      padding: 9px 20px;
      display: flex;
      align-items: center;
      gap: 8px;
      flex-wrap: wrap;
      flex-shrink: 0;
    }
    .filter-label {
      font-size: 0.68rem;
      font-weight: 700;
      text-transform: uppercase;
      letter-spacing: 0.07em;
      color: #9ca3af;
      margin-right: 2px;
    }
    .filter-group { display: flex; gap: 5px; flex-wrap: wrap; }
    .pill {
      padding: 4px 11px;
      border-radius: 999px;
      border: 1px solid #d1d5db;
      background: #fff;
      color: #6b7280;
      font-size: 0.75rem;
      cursor: pointer;
      transition: background 0.12s, color 0.12s, border-color 0.12s;
      white-space: nowrap;
    }
    .pill:hover { background: #f3f4f6; color: #111; border-color: #9ca3af; }
    .pill.active {
      background: #2563eb;
      border-color: #2563eb;
      color: #fff;
      font-weight: 600;
    }
    .divider {
      width: 1px;
      height: 18px;
      background: #e5e7eb;
      flex-shrink: 0;
    }

    /* ── Main Layout ── */
    .main {
      display: flex;
      flex: 1;
      overflow: hidden;
    }

    /* ── Sidebar ── */
    .sidebar {
      width: 300px;
      flex-shrink: 0;
      background: #fff;
      border-right: 1px solid #e5e7eb;
      overflow-y: auto;
      display: flex;
      flex-direction: column;
    }
    .sidebar::-webkit-scrollbar { width: 5px; }
    .sidebar::-webkit-scrollbar-track { background: #f9fafb; }
    .sidebar::-webkit-scrollbar-thumb { background: #d1d5db; border-radius: 3px; }

    .event-card {
      padding: 13px 16px;
      border-bottom: 1px solid #f3f4f6;
      cursor: pointer;
      transition: background 0.1s;
    }
    .event-card:hover { background: #f9fafb; }
    .event-card.selected {
      background: #eff6ff;
      border-left: 3px solid #2563eb;
    }
    .event-card .name {
      font-size: 0.88rem;
      font-weight: 600;
      color: #111;
      margin-bottom: 3px;
      line-height: 1.35;
    }
    .event-card .datetime {
      font-size: 0.75rem;
      color: #2563eb;
      margin-bottom: 3px;
      display: flex;
      align-items: center;
      gap: 4px;
    }
    .event-card .location {
      font-size: 0.73rem;
      color: #9ca3af;
      line-height: 1.4;
    }
    .event-card .location::before { content: "📍 "; }

    .no-events {
      padding: 40px 20px;
      text-align: center;
      color: #9ca3af;
      font-size: 0.88rem;
      line-height: 1.6;
    }
    .no-events .emoji { font-size: 1.8rem; display: block; margin-bottom: 10px; }

    /* ── Map ── */
    #map { flex: 1; }

    /* ── Custom Info Window ── */
    .iw-outer {
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
      width: 260px;
      padding: 2px 4px 4px;
    }
    .iw-name {
      font-weight: 700;
      font-size: 0.95rem;
      color: #111;
      margin-bottom: 5px;
      line-height: 1.3;
    }
    .iw-date {
      font-size: 0.78rem;
      color: #2563eb;
      font-weight: 600;
      margin-bottom: 4px;
    }
    .iw-location {
      font-size: 0.75rem;
      color: #6b7280;
      margin-bottom: 8px;
    }
    .iw-divider {
      height: 1px;
      background: #e5e7eb;
      margin: 8px 0;
    }
    .iw-description {
      font-size: 0.78rem;
      color: #374151;
      line-height: 1.55;
      max-height: 160px;
      overflow-y: auto;
      white-space: pre-line;
    }
    .iw-description::-webkit-scrollbar { width: 4px; }
    .iw-description::-webkit-scrollbar-thumb { background: #d1d5db; border-radius: 2px; }
    .iw-description a { color: #2563eb; text-decoration: underline; }
    .iw-description a:hover { color: #1d4ed8; }

    /* ── Loading / Error overlay ── */
    #overlay {
      position: fixed;
      inset: 0;
      background: rgba(247,248,250,0.93);
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      gap: 14px;
      z-index: 999;
    }
    #overlay.hidden { display: none; }
    .spinner {
      width: 32px; height: 32px;
      border: 3px solid #e5e7eb;
      border-top-color: #2563eb;
      border-radius: 50%;
      animation: spin 0.75s linear infinite;
    }
    @keyframes spin { to { transform: rotate(360deg); } }
    #overlay p { color: #6b7280; font-size: 0.88rem; }
    #overlay .error-box {
      background: #fff;
      border: 1px solid #fca5a5;
      border-radius: 10px;
      padding: 20px 24px;
      max-width: 420px;
      text-align: center;
      display: none;
      box-shadow: 0 4px 16px rgba(0,0,0,0.08);
    }
    #overlay .error-box.visible { display: block; }
    #overlay .error-box h2 { color: #dc2626; margin-bottom: 8px; font-size: 1rem; }
    #overlay .error-box p { color: #6b7280; font-size: 0.83rem; line-height: 1.6; }
    #overlay .error-box code {
      display: inline-block;
      background: #f3f4f6;
      border: 1px solid #e5e7eb;
      border-radius: 4px;
      padding: 2px 6px;
      font-size: 0.8rem;
      color: #2563eb;
      margin-top: 5px;
    }

    /* ── Config Banner ── */
    #config-banner {
      background: #fffbeb;
      border-bottom: 2px solid #f59e0b;
      padding: 9px 20px;
      font-size: 0.8rem;
      color: #78350f;
      line-height: 1.5;
      display: none;
      flex-shrink: 0;
    }
    #config-banner code { color: #b45309; background: #fef3c7; padding: 1px 5px; border-radius: 3px; }
    #config-banner a { color: #b45309; }

    /* ── Day badges ── */
    .day-badge {
      display: inline-block;
      padding: 1px 6px;
      border-radius: 4px;
      font-size: 0.66rem;
      font-weight: 700;
      text-transform: uppercase;
      letter-spacing: 0.04em;
      margin-right: 3px;
    }
    .day-0 { background: #f0fdf4; color: #16a34a; }
    .day-1 { background: #eff6ff; color: #2563eb; }
    .day-2 { background: #faf5ff; color: #9333ea; }
    .day-3 { background: #f0fdf4; color: #059669; }
    .day-4 { background: #fffbeb; color: #d97706; }
    .day-5 { background: #fff7ed; color: #ea580c; }
    .day-6 { background: #fdf2f8; color: #db2777; }
  </style>
</head>
<body>

<!-- ══════════════════════════════════════════
     CONFIGURATION — edit these three values
     ══════════════════════════════════════════ -->
<script>
  const CONFIG = {
    // Your Google Calendar ID (e.g. "abc123@group.calendar.google.com"
    // or your Gmail address for your primary calendar)
    CALENDAR_ID: "c_aeda29041cfe2440600b99f6a79cc4070b65df0401d7cec4feb668d9e6a52982@group.calendar.google.com",

    // A Google API key with Calendar API + Maps JavaScript API + Geocoding API enabled
    // Get one at: https://console.cloud.google.com/apis/credentials
    GOOGLE_API_KEY: "AIzaSyCQUzIt4zc87z-S0Ub_aGJ2XFQd2mVg3oc",

    // How many days ahead to show events (default: 90)
    DAYS_AHEAD: 90,
  };
</script>
<!-- ══════════════════════════════════════════ -->

<!-- Loading / Error overlay -->
<div id="overlay">
  <div class="spinner" id="spinner"></div>
  <p id="overlay-msg">Loading open mics…</p>
  <div class="error-box" id="error-box">
    <h2>⚠️ Setup needed</h2>
    <p id="error-detail"></p>
  </div>
</div>

<div id="config-banner">
  📝 <strong>Setup required:</strong> Open this file in a text editor and replace
  <code>YOUR_CALENDAR_ID</code> and <code>YOUR_GOOGLE_API_KEY</code> in the CONFIG block near the top.
  See <a href="https://console.cloud.google.com/apis/credentials" target="_blank">Google Cloud Console</a>
  to create an API key (enable Calendar API + Maps JavaScript API + Geocoding API).
</div>

<header>
  <span class="mic-icon">🎤</span>
  <h1>Comedy Open Mics</h1>
  <span class="event-count" id="event-count">Loading…</span>
</header>

<div class="filter-bar">
  <span class="filter-label">Day</span>
  <div class="filter-group" id="day-filters">
    <button class="pill active" data-day="all">All</button>
    <button class="pill" data-day="0">Sun</button>
    <button class="pill" data-day="1">Mon</button>
    <button class="pill" data-day="2">Tue</button>
    <button class="pill" data-day="3">Wed</button>
    <button class="pill" data-day="4">Thu</button>
    <button class="pill" data-day="5">Fri</button>
    <button class="pill" data-day="6">Sat</button>
  </div>

  <div class="divider"></div>

  <span class="filter-label">Time</span>
  <div class="filter-group" id="time-filters">
    <button class="pill active" data-time="all">All</button>
    <button class="pill" data-time="morning" title="Before noon">Morning</button>
    <button class="pill" data-time="afternoon" title="Noon–6pm">Afternoon</button>
    <button class="pill" data-time="evening" title="After 6pm">Evening</button>
  </div>

  <div class="divider"></div>

  <span class="filter-label">Range</span>
  <div class="filter-group" id="range-filters">
    <button class="pill active" data-range="all">All upcoming</button>
    <button class="pill" data-range="7">This week</button>
    <button class="pill" data-range="14">2 weeks</button>
    <button class="pill" data-range="30">Month</button>
  </div>
</div>

<div class="main">
  <div class="sidebar" id="sidebar"></div>
  <div id="map"></div>
</div>

<script>
  // ── State ──────────────────────────────────────────────────────────────────
  let allEvents = [];
  let markers = [];
  let infoWindow = null;
  let map = null;
  let selectedCardEl = null;
  let activeDay = "all";
  let activeTime = "all";
  let activeRange = "all";

  const DAY_SHORT = ["Sun","Mon","Tue","Wed","Thu","Fri","Sat"];

  // ── Helpers ────────────────────────────────────────────────────────────────
  function showOverlay(msg) {
    document.getElementById("overlay").classList.remove("hidden");
    document.getElementById("spinner").style.display = "block";
    document.getElementById("overlay-msg").textContent = msg;
    document.getElementById("error-box").classList.remove("visible");
  }

  function showError(msg) {
    document.getElementById("overlay").classList.remove("hidden");
    document.getElementById("spinner").style.display = "none";
    document.getElementById("overlay-msg").textContent = "";
    const box = document.getElementById("error-box");
    box.classList.add("visible");
    document.getElementById("error-detail").innerHTML = msg;
  }

  function hideOverlay() {
    document.getElementById("overlay").classList.add("hidden");
  }

  function formatDate(date) {
    return date.toLocaleDateString(undefined, { weekday: "short", month: "short", day: "numeric" });
  }

  function formatTime(date) {
    return date.toLocaleTimeString(undefined, { hour: "numeric", minute: "2-digit" });
  }

  function timeCategory(date) {
    const h = date.getHours() + date.getMinutes() / 60;
    if (h < 12) return "morning";
    if (h < 18) return "afternoon";
    return "evening";
  }

  function dayBadge(date) {
    const d = date.getDay();
    return `<span class="day-badge day-${d}">${DAY_SHORT[d]}</span>`;
  }

  // Clean description HTML: preserve links, convert block tags to line breaks, strip the rest
  function cleanDescription(html) {
    if (!html) return "";
    return html
      // Ensure links open in new tab and are safe
      .replace(/<a\s+([^>]*href=["'][^"']*["'][^>]*)>/gi, (match, attrs) => {
        // Strip any existing target/rel, then add our own
        const href = (attrs.match(/href=["']([^"']*)["']/) || [])[1] || "";
        return `<a href="${href}" target="_blank" rel="noopener noreferrer">`;
      })
      // Convert block-level tags to line breaks
      .replace(/<br\s*\/?>/gi, "\n")
      .replace(/<\/p>/gi, "\n")
      .replace(/<p[^>]*>/gi, "")
      // Strip all remaining tags except <a> and </a>
      .replace(/<(?!\/?a\b)[^>]+>/g, "")
      // Decode common entities
      .replace(/&amp;/g, "&")
      .replace(/&lt;/g, "<")
      .replace(/&gt;/g, ">")
      .replace(/&nbsp;/g, " ")
      .replace(/&quot;/g, '"')
      .trim();
  }

  // ── Filtering ──────────────────────────────────────────────────────────────
  function getFilteredEvents() {
    const now = new Date();
    return allEvents.filter(ev => {
      const start = ev.startDate;
      if (start < now) return false;
      if (activeRange !== "all") {
        const cutoff = new Date(now.getTime() + activeRange * 86400000);
        if (start > cutoff) return false;
      }
      if (activeDay !== "all" && start.getDay() !== parseInt(activeDay)) return false;
      if (activeTime !== "all" && timeCategory(start) !== activeTime) return false;
      return true;
    });
  }

  // ── Render sidebar ─────────────────────────────────────────────────────────
  function renderSidebar(events) {
    const sidebar = document.getElementById("sidebar");
    sidebar.innerHTML = "";

    const count = document.getElementById("event-count");
    count.textContent = events.length
      ? `${events.length} open mic${events.length === 1 ? "" : "s"}`
      : "";

    if (!events.length) {
      sidebar.innerHTML = `<div class="no-events">
        <span class="emoji">🎙️</span>
        No open mics match your filters.
      </div>`;
      return;
    }

    events.forEach((ev, i) => {
      const card = document.createElement("div");
      card.className = "event-card";
      card.dataset.index = i;
      card.innerHTML = `
        <div class="name">${ev.summary}</div>
        <div class="datetime">${dayBadge(ev.startDate)}${formatDate(ev.startDate)} · ${formatTime(ev.startDate)}</div>
        ${ev.location ? `<div class="location">${ev.location}</div>` : ""}
      `;
      card.addEventListener("click", () => selectEvent(ev, card));
      sidebar.appendChild(card);
    });
  }

  // ── Build info window HTML ─────────────────────────────────────────────────
  function buildInfoWindowContent(ev) {
    const desc = cleanDescription(ev.description);
    return `
      <div class="iw-outer">
        <div class="iw-name">${ev.summary}</div>
        <div class="iw-date">${formatDate(ev.startDate)} · ${formatTime(ev.startDate)}</div>
        ${ev.location ? `<div class="iw-location">📍 ${ev.location}</div>` : ""}
        ${desc ? `<div class="iw-divider"></div><div class="iw-description">${desc}</div>` : ""}
      </div>
    `;
  }

  // ── Render map markers ────────────────────────────────────────────────────
  function renderMarkers(events) {
    markers.forEach(m => m.setMap(null));
    markers = [];
    if (infoWindow) infoWindow.close();

    const bounds = new google.maps.LatLngBounds();
    let hasGeo = false;

    events.forEach(ev => {
      if (!ev.lat || !ev.lng) return;
      hasGeo = true;
      const pos = { lat: ev.lat, lng: ev.lng };
      bounds.extend(pos);

      const marker = new google.maps.Marker({
        position: pos,
        map,
        title: ev.summary,
        icon: {
          path: google.maps.SymbolPath.CIRCLE,
          scale: 9,
          fillColor: "#2563eb",
          fillOpacity: 1,
          strokeColor: "#fff",
          strokeWeight: 2.5,
        },
      });

      marker.addListener("click", () => {
        const card = document.querySelector(`.event-card[data-index="${events.indexOf(ev)}"]`);
        selectEvent(ev, card, marker);
      });

      markers.push(marker);
      ev._marker = marker;
    });

    if (hasGeo) map.fitBounds(bounds, { padding: 60 });
  }

  // ── Select an event ───────────────────────────────────────────────────────
  function selectEvent(ev, cardEl, markerOverride) {
    if (selectedCardEl) selectedCardEl.classList.remove("selected");
    if (cardEl) {
      cardEl.classList.add("selected");
      cardEl.scrollIntoView({ behavior: "smooth", block: "nearest" });
    }
    selectedCardEl = cardEl;

    if (!ev.lat || !ev.lng) return;

    if (!infoWindow) infoWindow = new google.maps.InfoWindow({ maxWidth: 280 });
    const marker = markerOverride || ev._marker;
    infoWindow.setContent(buildInfoWindowContent(ev));
    infoWindow.open(map, marker);
    map.panTo({ lat: ev.lat, lng: ev.lng });
  }

  // ── Apply filters & re-render ─────────────────────────────────────────────
  function applyFilters() {
    const filtered = getFilteredEvents();
    renderSidebar(filtered);
    renderMarkers(filtered);
  }

  // ── Filter pill handlers ──────────────────────────────────────────────────
  function setupFilters() {
    ["day-filters", "time-filters", "range-filters"].forEach(groupId => {
      const key = groupId.replace("-filters", "");
      document.querySelectorAll(`#${groupId} .pill`).forEach(btn => {
        btn.addEventListener("click", () => {
          document.querySelectorAll(`#${groupId} .pill`).forEach(b => b.classList.remove("active"));
          btn.classList.add("active");
          const val = btn.dataset[key] ?? btn.dataset.day ?? btn.dataset.time ?? btn.dataset.range;
          if (key === "day") activeDay = val;
          else if (key === "time") activeTime = val;
          else if (key === "range") activeRange = val === "all" ? "all" : parseInt(val);
          applyFilters();
        });
      });
    });
  }

  // ── Geocode an address ────────────────────────────────────────────────────
  async function geocode(address) {
    const url = `https://maps.googleapis.com/maps/api/geocode/json?address=${encodeURIComponent(address)}&key=${CONFIG.GOOGLE_API_KEY}`;
    const res = await fetch(url);
    const data = await res.json();
    if (data.status === "OK" && data.results.length) {
      const loc = data.results[0].geometry.location;
      return { lat: loc.lat, lng: loc.lng };
    }
    return null;
  }

  // ── Fetch events from Google Calendar API ─────────────────────────────────
  async function fetchEvents() {
    const timeMin = new Date().toISOString();
    const timeMax = new Date(Date.now() + CONFIG.DAYS_AHEAD * 86400000).toISOString();
    const url = `https://www.googleapis.com/calendar/v3/calendars/${encodeURIComponent(CONFIG.CALENDAR_ID)}/events`
      + `?key=${CONFIG.GOOGLE_API_KEY}`
      + `&timeMin=${timeMin}&timeMax=${timeMax}`
      + `&singleEvents=true&orderBy=startTime&maxResults=250`;

    const res = await fetch(url);
    if (!res.ok) {
      const err = await res.json().catch(() => ({}));
      throw new Error(err?.error?.message || `HTTP ${res.status}`);
    }
    const data = await res.json();
    return data.items || [];
  }

  // ── Bootstrap ─────────────────────────────────────────────────────────────
  async function initApp() {
    if (CONFIG.CALENDAR_ID === "YOUR_CALENDAR_ID" || CONFIG.GOOGLE_API_KEY === "YOUR_GOOGLE_API_KEY") {
      document.getElementById("config-banner").style.display = "block";
      showError(
        `Open this HTML file in a text editor and fill in your <code>CALENDAR_ID</code> and <code>GOOGLE_API_KEY</code> in the CONFIG block near the top.<br><br>
        See <a href="https://console.cloud.google.com/apis/credentials" target="_blank" style="color:#2563eb">Google Cloud Console</a> to get a free API key.`
      );
      return;
    }

    showOverlay("Loading open mics…");

    let rawEvents;
    try {
      rawEvents = await fetchEvents();
    } catch (e) {
      showError(`Could not fetch calendar events: <strong>${e.message}</strong><br><br>
        Make sure your calendar is public, the Calendar API is enabled, and your <code>CALENDAR_ID</code> is correct.`);
      return;
    }

    if (!rawEvents.length) {
      hideOverlay();
      document.getElementById("event-count").textContent = "No upcoming events";
      document.getElementById("sidebar").innerHTML =
        `<div class="no-events"><span class="emoji">🎙️</span>No upcoming events found.</div>`;
      return;
    }

    const parsed = rawEvents.map(item => ({
      id: item.id,
      summary: item.summary || "Open Mic",
      location: item.location || "",
      description: item.description || "",
      startDate: new Date(item.start?.dateTime || item.start?.date),
      endDate:   new Date(item.end?.dateTime   || item.end?.date),
      lat: null,
      lng: null,
    }));

    showOverlay("Geocoding locations…");
    const batchSize = 5;
    for (let i = 0; i < parsed.length; i += batchSize) {
      await Promise.all(parsed.slice(i, i + batchSize).map(async ev => {
        if (ev.location) {
          const geo = await geocode(ev.location);
          if (geo) { ev.lat = geo.lat; ev.lng = geo.lng; }
        }
      }));
    }

    allEvents = parsed;
    hideOverlay();
    setupFilters();
    applyFilters();
  }

  // ── Google Maps init callback ─────────────────────────────────────────────
  window.initMap = function () {
    map = new google.maps.Map(document.getElementById("map"), {
      center: { lat: 40.7128, lng: -74.006 },
      zoom: 11,
      disableDefaultUI: true,          // remove all default controls
      zoomControl: true,               // add back only zoom
      zoomControlOptions: {
        position: google.maps.ControlPosition.RIGHT_BOTTOM,
      },
      gestureHandling: "greedy",
      styles: [
        // Hide most POIs and transit clutter
        { featureType: "poi", stylers: [{ visibility: "off" }] },
        { featureType: "transit", stylers: [{ visibility: "off" }] },
        { featureType: "road", elementType: "labels.icon", stylers: [{ visibility: "off" }] },
        // Subtle color palette
        { featureType: "water", elementType: "geometry", stylers: [{ color: "#c9e8f5" }] },
        { featureType: "landscape", elementType: "geometry", stylers: [{ color: "#f5f5f5" }] },
        { featureType: "road.highway", elementType: "geometry", stylers: [{ color: "#e0e0e0" }] },
        { featureType: "road.arterial", elementType: "geometry", stylers: [{ color: "#ebebeb" }] },
        { featureType: "road.local", elementType: "geometry", stylers: [{ color: "#f5f5f5" }] },
        { featureType: "administrative.locality", elementType: "labels.text.fill", stylers: [{ color: "#555" }] },
        { featureType: "road", elementType: "labels.text.fill", stylers: [{ color: "#888" }] },
      ],
    });
    initApp();
  };
</script>

<script>
  (function () {
    const key = CONFIG.GOOGLE_API_KEY === "YOUR_GOOGLE_API_KEY" ? "" : CONFIG.GOOGLE_API_KEY;
    const s = document.createElement("script");
    s.src = `https://maps.googleapis.com/maps/api/js?key=${key}&callback=initMap`;
    s.async = true; s.defer = true;
    s.onerror = () => showError("Failed to load Google Maps. Check that your API key has the <strong>Maps JavaScript API</strong> enabled.");
    document.head.appendChild(s);
  })();
</script>

</body>
</html>
