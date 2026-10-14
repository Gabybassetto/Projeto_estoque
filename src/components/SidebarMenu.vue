<script setup>
defineProps({
  activeSection: { type: String, required: true },
  lowStockCount: { type: Number, required: true },
  outOfStockCount: { type: Number, required: true },
})

defineEmits(['change-section'])

const sections = [
  { id: 'overview', label: 'Visão geral', icon: 'overview' },
  { id: 'products', label: 'Produtos', icon: 'products' },
  { id: 'low', label: 'Estoque baixo', icon: 'low', count: 'lowStockCount' },
  { id: 'out', label: 'Sem estoque', icon: 'out', count: 'outOfStockCount' },
]
</script>

<template>
  <aside class="sidebar">
    <div class="sidebar-caption">MENU PRINCIPAL</div>
    <nav class="side-nav" aria-label="Navegação principal">
      <button
        v-for="section in sections"
        :key="section.id"
        class="nav-link"
        :class="{ 'nav-link-active': activeSection === section.id }"
        type="button"
        :aria-current="activeSection === section.id ? 'page' : undefined"
        @click="$emit('change-section', section.id)"
      >
        <svg v-if="section.icon === 'overview'" viewBox="0 0 20 20" fill="none" aria-hidden="true"><rect x="3" y="3" width="5.5" height="5.5" rx="1.3" stroke="currentColor" stroke-width="1.5"/><rect x="11.5" y="3" width="5.5" height="5.5" rx="1.3" stroke="currentColor" stroke-width="1.5"/><rect x="3" y="11.5" width="5.5" height="5.5" rx="1.3" stroke="currentColor" stroke-width="1.5"/><rect x="11.5" y="11.5" width="5.5" height="5.5" rx="1.3" stroke="currentColor" stroke-width="1.5"/></svg>
        <svg v-else-if="section.icon === 'products'" viewBox="0 0 20 20" fill="none" aria-hidden="true"><path d="M3.5 6.5h13v10h-13zM2.5 3.5h15v3h-15zM7 10h6" stroke="currentColor" stroke-width="1.5" stroke-linejoin="round" stroke-linecap="round"/></svg>
        <svg v-else-if="section.icon === 'low'" viewBox="0 0 20 20" fill="none" aria-hidden="true"><path d="M10 3 18 17H2L10 3Z" stroke="currentColor" stroke-width="1.5" stroke-linejoin="round"/><path d="M10 8v4m0 2h.01" stroke="currentColor" stroke-width="1.6" stroke-linecap="round"/></svg>
        <svg v-else viewBox="0 0 20 20" fill="none" aria-hidden="true"><circle cx="10" cy="10" r="7" stroke="currentColor" stroke-width="1.5"/><path d="m7 7 6 6m0-6-6 6" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/></svg>
        <span>{{ section.label }}</span>
        <span v-if="section.count" class="nav-count">{{ section.count === 'lowStockCount' ? lowStockCount : outOfStockCount }}</span>
      </button>
    </nav>
    <div class="sidebar-bottom">
      <div class="sidebar-status"><span class="status-dot"></span><span>Estoque atualizado</span></div>
      <span class="sidebar-version">STOCKLY · PROJETO ACADÊMICO</span>
    </div>
  </aside>
</template>