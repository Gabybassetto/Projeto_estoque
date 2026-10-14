<script setup>
import { computed, ref } from 'vue'

const props = defineProps({
  products: { type: Array, required: true },
  categories: { type: Array, required: true },
  quickFilter: { type: String, required: true },
})

defineEmits(['edit-product', 'delete-product', 'change-quantity', 'change-filter'])

const search = ref('')
const category = ref('all')
const showFilters = ref(true)

const money = new Intl.NumberFormat('pt-BR', { style: 'currency', currency: 'BRL' })

function getStatus(product) {
  if (product.quantity === 0) return 'out'
  if (product.quantity <= product.minimum) return 'low'
  return 'normal'
}

const visibleProducts = computed(() => {
  const term = search.value.trim().toLocaleLowerCase('pt-BR')
  return props.products.filter((product) => {
    const matchesSearch = !term || product.name.toLocaleLowerCase('pt-BR').includes(term) || product.sku.toLocaleLowerCase('pt-BR').includes(term)
    const matchesCategory = category.value === 'all' || product.category === category.value
    const matchesStatus = props.quickFilter === 'all' || getStatus(product) === props.quickFilter
    return matchesSearch && matchesCategory && matchesStatus
  })
})

function formatPrice(value) {
  return money.format(value)
}
</script>

<template>
  <section id="produtos" class="table-panel" aria-labelledby="products-heading">
    <div class="table-heading">
      <div>
        <h2 id="products-heading">Produtos cadastrados</h2>
        <p>Acompanhe e atualize os itens do seu estoque.</p>
      </div>
      <button class="button button-filter" type="button" :aria-expanded="showFilters" @click="showFilters = !showFilters">
        <svg viewBox="0 0 20 20" fill="none" aria-hidden="true"><path d="M3 5h14M5.5 10h9M8 15h4" stroke="currentColor" stroke-width="1.6" stroke-linecap="round"/></svg>
        <span>Filtros</span>
      </button>
    </div>

    <div v-show="showFilters" class="filters-row">
      <label class="search-field">
        <svg viewBox="0 0 20 20" fill="none" aria-hidden="true"><circle cx="8.8" cy="8.8" r="5.8" stroke="currentColor" stroke-width="1.5"/><path d="m13 13 4 4" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/></svg>
        <input v-model="search" type="search" placeholder="Pesquisar produtos ou SKU" aria-label="Pesquisar produtos pelo nome ou código SKU" />
      </label>
      <label class="select-field">
        <span class="sr-only">Filtrar por categoria</span>
        <select v-model="category">
          <option value="all">Todas as categorias</option>
          <option v-for="item in categories" :key="item" :value="item">{{ item }}</option>
        </select>
      </label>
      <label class="select-field">
        <span class="sr-only">Filtrar por situação do estoque</span>
        <select :value="quickFilter" @change="$emit('change-filter', $event.target.value)">
          <option value="all">Todas as situações</option>
          <option value="normal">Em estoque</option>
          <option value="low">Estoque baixo</option>
          <option value="out">Sem estoque</option>
        </select>
      </label>
    </div>

    <div class="table-scroll">
      <table class="product-table">
        <thead>
          <tr><th>Produto</th><th>Categoria</th><th>Quantidade</th><th>Qtd. mínima</th><th>Preço unitário</th><th>Situação</th><th><span class="sr-only">Ações</span></th></tr>
        </thead>
        <tbody>
          <tr v-for="product in visibleProducts" :key="product.id">
            <td data-label="Produto">
              <div class="product-name">{{ product.name }}</div>
              <div class="product-sku">SKU {{ product.sku }}</div>
            </td>
            <td data-label="Categoria"><span class="category-label">{{ product.category }}</span></td>
            <td data-label="Quantidade">
              <div class="quantity-control">
                <button type="button" :aria-label="`Diminuir quantidade de ${product.name}`" :disabled="product.quantity === 0" @click="$emit('change-quantity', product.id, -1)">−</button>
                <strong>{{ product.quantity }}</strong>
                <button type="button" :aria-label="`Aumentar quantidade de ${product.name}`" @click="$emit('change-quantity', product.id, 1)">+</button>
              </div>
            </td>
            <td data-label="Qtd. mínima" class="muted-cell">{{ product.minimum }}</td>
            <td data-label="Preço unitário" class="price-cell">{{ formatPrice(product.price) }}</td>
            <td data-label="Situação"><span class="stock-badge" :class="`badge-${getStatus(product)}`"><span></span>{{ getStatus(product) === 'normal' ? 'Em estoque' : getStatus(product) === 'low' ? 'Estoque baixo' : 'Sem estoque' }}</span></td>
            <td data-label="Ações">
              <div class="row-actions">
                <button class="icon-button" type="button" :aria-label="`Editar ${product.name}`" title="Editar produto" @click="$emit('edit-product', product)"><svg viewBox="0 0 20 20" fill="none" aria-hidden="true"><path d="m12.5 4.2 3.3 3.3M4 16l3.4-.7 8.8-8.8a1.7 1.7 0 0 0-2.4-2.4L5 12.9 4 16Z" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/></svg></button>
                <button class="icon-button icon-button-danger" type="button" :aria-label="`Excluir ${product.name}`" title="Excluir produto" @click="$emit('delete-product', product)"><svg viewBox="0 0 20 20" fill="none" aria-hidden="true"><path d="M4.5 6h11m-9.5 0 .7 10h7.6L15 6M8 6V4h4v2m-3 3v4m2-4v4" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/></svg></button>
              </div>
            </td>
          </tr>
          <tr v-if="visibleProducts.length === 0"><td class="empty-state" colspan="7"><span class="empty-title">Nenhum produto encontrado</span><span>Tente ajustar a pesquisa ou os filtros selecionados.</span></td></tr>
        </tbody>
      </table>
    </div>
    <div class="table-footer"><span>Exibindo <strong>{{ visibleProducts.length }}</strong> de <strong>{{ products.length }}</strong> produtos</span><span class="footer-live"><i></i> Dados atualizados em tempo real</span></div>
  </section>
</template>