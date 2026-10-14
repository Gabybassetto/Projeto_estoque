<script setup>
import { computed, ref, watch } from 'vue'
import AppHeader from './components/AppHeader.vue'
import SidebarMenu from './components/SidebarMenu.vue'
import StatCard from './components/StatCard.vue'
import ProductTable from './components/ProductTable.vue'
import ProductForm from './components/ProductForm.vue'

const STORAGE_KEY = 'stockly-products'
const initialProducts = [
  { id: 1, name: 'Cimento CP II 50 kg', sku: 'CIM-001', category: 'Cimento e agregados', quantity: 42, minimum: 15, price: 39.9 },
  { id: 2, name: 'Vergalhão CA-50 10 mm', sku: 'VER-002', category: 'Aço e metais', quantity: 8, minimum: 12, price: 48.5 },
  { id: 3, name: 'Areia média ensacada 20 kg', sku: 'ARE-003', category: 'Cimento e agregados', quantity: 60, minimum: 20, price: 8.9 },
  { id: 4, name: 'Tijolo cerâmico 8 furos', sku: 'TIJ-004', category: 'Alvenaria', quantity: 0, minimum: 100, price: 1.25 },
  { id: 5, name: 'Tubo PVC soldável 25 mm', sku: 'PVC-005', category: 'Hidráulica', quantity: 5, minimum: 10, price: 12.9 },
  { id: 6, name: 'Furadeira de impacto', sku: 'FUR-006', category: 'Ferramentas', quantity: 7, minimum: 3, price: 289.9 },
]

const activeSection = ref('overview')
const showForm = ref(false)
const editingProduct = ref(null)
const quickFilter = ref('all')
const showNotice = ref(true)
const darkMode = ref(true)
const persistenceError = ref('')
const products = ref(loadProducts())
const categories = computed(() => [...new Set(products.value.map((product) => product.category))].sort((a, b) => a.localeCompare(b, 'pt-BR')))
const lowStockCount = computed(() => products.value.filter((product) => product.quantity > 0 && product.quantity <= product.minimum).length)
const outOfStockCount = computed(() => products.value.filter((product) => product.quantity === 0).length)
const totalUnits = computed(() => products.value.reduce((total, product) => total + product.quantity, 0))
const dateLabel = new Intl.DateTimeFormat('pt-BR', { dateStyle: 'full' }).format(new Date())

function isStoredProduct(product) {
  return product !== null
    && typeof product === 'object'
    && Number.isSafeInteger(product.id)
    && typeof product.name === 'string'
    && typeof product.sku === 'string'
    && typeof product.category === 'string'
    && Number.isFinite(product.quantity)
    && product.quantity >= 0
    && Number.isInteger(product.quantity)
    && Number.isFinite(product.minimum)
    && product.minimum >= 0
    && Number.isInteger(product.minimum)
    && Number.isFinite(product.price)
    && product.price >= 0
}

function loadProducts() {
  try {
    const savedProducts = window.localStorage.getItem(STORAGE_KEY)
    if (savedProducts === null) return initialProducts

    const parsedProducts = JSON.parse(savedProducts)
    if (Array.isArray(parsedProducts) && parsedProducts.every(isStoredProduct)) return parsedProducts

    persistenceError.value = 'Os dados salvos do estoque estão em um formato inválido. Os dados de demonstração foram carregados; as alterações feitas agora substituirão o conteúdo salvo.'
  } catch {
    persistenceError.value = 'Não foi possível acessar os dados salvos. Os dados de demonstração foram carregados e não é possível garantir o salvamento nesta sessão.'
  }

  return initialProducts
}

function persistProducts() {
  try {
    window.localStorage.setItem(STORAGE_KEY, JSON.stringify(products.value))
    persistenceError.value = ''
  } catch {
    persistenceError.value = 'Não foi possível salvar as alterações neste navegador. Elas podem ser perdidas ao fechar ou atualizar a página.'
  }
}

watch(products, persistProducts, { deep: true })

const stats = computed(() => [
  { title: 'Produtos cadastrados', value: products.value.length, tone: 'blue', icon: 'box', detail: 'Itens no catálogo' },
  { title: 'Unidades em estoque', value: totalUnits.value, tone: 'green', icon: 'units', detail: 'Unidades disponíveis' },
  { title: 'Estoque baixo', value: lowStockCount.value, tone: 'orange', icon: 'low', detail: 'Abaixo ou no mínimo' },
  { title: 'Sem estoque', value: outOfStockCount.value, tone: 'red', icon: 'out', detail: 'Precisam de reposição' },
])

function startNewProduct() {
  editingProduct.value = null
  showForm.value = true
}

function editProduct(product) {
  editingProduct.value = { ...product }
  showForm.value = true
}

function saveProduct(productData) {
  if (editingProduct.value) {
    const index = products.value.findIndex((item) => item.id === editingProduct.value.id)
    if (index !== -1) products.value[index] = { ...products.value[index], ...productData }
  } else {
    products.value.push({ id: Date.now(), ...productData })
  }
  showForm.value = false
  editingProduct.value = null
}

function deleteProduct(product) {
  if (window.confirm(`Deseja excluir o produto "${product.name}"?`)) {
    products.value = products.value.filter((item) => item.id !== product.id)
  }
}

function changeQuantity(productId, amount) {
  const product = products.value.find((item) => item.id === productId)
  if (product) product.quantity = Math.max(0, product.quantity + amount)
}

function changeSection(section) {
  activeSection.value = section
  quickFilter.value = section === 'low' ? 'low' : section === 'out' ? 'out' : 'all'
  const target = section === 'overview' ? 'inicio' : 'produtos'
  document.getElementById(target)?.scrollIntoView({ behavior: 'smooth', block: 'start' })
}
</script>

<template>
  <div id="inicio" class="app-shell" :class="{ 'theme-dark': darkMode }">
    <AppHeader :date-label="dateLabel" :is-dark="darkMode" @new-product="startNewProduct" @toggle-theme="darkMode = !darkMode" />
    <div class="workspace">
      <SidebarMenu :active-section="activeSection" :low-stock-count="lowStockCount" :out-of-stock-count="outOfStockCount" @change-section="changeSection" />
      <main class="main-content">
        <section class="page-intro">
          <div><span class="eyebrow">PAINEL DE CONTROLE</span><h1>Visão geral do estoque</h1><p>Acompanhe seus produtos e mantenha tudo em ordem.</p></div>
          <span class="inventory-chip"><i></i> Operação ativa</span>
        </section>

        <p v-if="persistenceError" class="persistence-warning" role="alert">{{ persistenceError }}</p>

        <section class="stats-grid" aria-label="Indicadores do estoque">
          <StatCard v-for="stat in stats" :key="stat.title" :title="stat.title" :value="stat.value" :tone="stat.tone" :icon="stat.icon" :detail="stat.detail" />
        </section>

        <section v-show="showNotice" class="notice-strip">
          <span class="notice-icon" aria-hidden="true"><svg viewBox="0 0 20 20" fill="none"><path d="M10 2.8 18 17H2l8-14.2Z" stroke="currentColor" stroke-width="1.4" stroke-linejoin="round"/><path d="M10 7v4m0 2.5h.01" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/></svg></span>
          <span><strong>Atenção ao estoque</strong> · {{ lowStockCount + outOfStockCount }} {{ lowStockCount + outOfStockCount === 1 ? 'produto precisa' : 'produtos precisam' }} de reposição.</span>
          <button class="notice-close" type="button" aria-label="Ocultar aviso" @click="showNotice = false">×</button>
        </section>

        <ProductTable :products="products" :categories="categories" :quick-filter="quickFilter" @edit-product="editProduct" @delete-product="deleteProduct" @change-quantity="changeQuantity" @change-filter="quickFilter = $event; activeSection = 'products'" />
        <footer class="page-footer"><span>Stockly <span class="footer-separator">·</span> Sistema de controle de estoque</span><span>Projeto acadêmico de Desenvolvimento Front-End</span></footer>
      </main>
    </div>
    <ProductForm v-if="showForm" :key="editingProduct?.id ?? 'novo'" :product="editingProduct" :categories="categories" :products="products" @save-product="saveProduct" @cancel="showForm = false; editingProduct = null" />
  </div>
</template>