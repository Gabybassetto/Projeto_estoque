<script setup>
defineProps({
  title: { type: String, required: true },
  value: { type: [String, Number], required: true },
  tone: { type: String, default: 'blue' },
  icon: { type: String, required: true },
  detail: { type: String, required: true },
})
</script>

<template>
  <article class="stat-card" :class="`stat-${tone}`">
    <div class="stat-topline">
      <span class="stat-label">{{ title }}</span>
      <span class="stat-icon" aria-hidden="true">
        <svg v-if="icon === 'box'" viewBox="0 0 24 24" fill="none"><path d="m12 3 8 4.5v9L12 21l-8-4.5v-9L12 3Z" stroke="currentColor" stroke-width="1.7" stroke-linejoin="round"/><path d="m4.5 7.8 7.5 4.3 7.5-4.3M12 12v8" stroke="currentColor" stroke-width="1.7" stroke-linejoin="round"/></svg>
        <svg v-else-if="icon === 'units'" viewBox="0 0 24 24" fill="none"><path d="M4 7h16M4 12h16M4 17h10" stroke="currentColor" stroke-width="1.8" stroke-linecap="round"/><circle cx="18" cy="17" r="2" stroke="currentColor" stroke-width="1.6"/></svg>
        <svg v-else-if="icon === 'low'" viewBox="0 0 24 24" fill="none"><path d="M12 3 22 20H2L12 3Z" stroke="currentColor" stroke-width="1.7" stroke-linejoin="round"/><path d="M12 9v4m0 3h.01" stroke="currentColor" stroke-width="1.8" stroke-linecap="round"/></svg>
        <svg v-else viewBox="0 0 24 24" fill="none"><circle cx="12" cy="12" r="9" stroke="currentColor" stroke-width="1.7"/><path d="m8.5 8.5 7 7m0-7-7 7" stroke="currentColor" stroke-width="1.7" stroke-linecap="round"/></svg>
      </span>
    </div>
    <strong class="stat-value">{{ value }}</strong>
    <span class="stat-detail">{{ detail }}</span>
  </article>
</template>