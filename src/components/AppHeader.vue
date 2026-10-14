<script setup>
defineProps({
  dateLabel: { type: String, required: true },
  isDark: { type: Boolean, required: true },
})

defineEmits(['new-product', 'toggle-theme'])
</script>

<template>
  <header class="topbar">
    <a class="brand" href="#inicio" aria-label="Stockly, início">
      <span class="brand-mark" aria-hidden="true">
        <svg viewBox="0 0 24 24" fill="none"><path d="M4 7.5 12 3l8 4.5v9L12 21l-8-4.5v-9Z" stroke="currentColor" stroke-width="1.8" stroke-linejoin="round"/><path d="m4.5 7.8 7.5 4.3 7.5-4.3M12 12.2V20" stroke="currentColor" stroke-width="1.8" stroke-linejoin="round"/></svg>
      </span>
      <span class="brand-copy"><strong>stockly</strong><small>Sistema de controle de estoque</small></span>
    </a>
    <div class="topbar-actions">
      <span class="date-label">{{ dateLabel }}</span>
      <button class="icon-button theme-toggle" type="button" :aria-label="isDark ? 'Ativar modo claro' : 'Ativar modo escuro'" :title="isDark ? 'Ativar modo claro' : 'Ativar modo escuro'" @click="$emit('toggle-theme')">
        <svg v-if="isDark" viewBox="0 0 20 20" fill="none" aria-hidden="true"><circle cx="10" cy="10" r="3.4" stroke="currentColor" stroke-width="1.5"/><path d="M10 2v1.5m0 13V18m8-8h-1.5m-13 0H2m13.66-5.66-1.06 1.06m-9.2 9.2-1.06 1.06m11.32 0-1.06-1.06m-9.2-9.2L4.34 4.34" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/></svg>
        <svg v-else viewBox="0 0 20 20" fill="none" aria-hidden="true"><path d="M16.2 12.2A6.8 6.8 0 0 1 7.8 3.8a6.8 6.8 0 1 0 8.4 8.4Z" stroke="currentColor" stroke-width="1.5" stroke-linejoin="round"/></svg>
      </button>
      <button class="button button-primary" type="button" @click="$emit('new-product')">
        <svg viewBox="0 0 20 20" fill="none" aria-hidden="true"><path d="M10 4v12M4 10h12" stroke="currentColor" stroke-width="1.8" stroke-linecap="round"/></svg>
        <span>Novo produto</span>
      </button>
    </div>
  </header>
</template>