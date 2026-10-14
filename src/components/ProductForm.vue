<script setup>
import { ref, watch } from 'vue'

const props = defineProps({
  product: { type: Object, default: null },
  categories: { type: Array, required: true },
  products: { type: Array, required: true },
})

const emit = defineEmits(['save-product', 'cancel'])

const name = ref('')
const sku = ref('')
const category = ref('')
const quantity = ref('0')
const minimum = ref('0')
const price = ref('0.00')
const errorMessage = ref('')

watch(() => props.product, (product) => {
  name.value = product?.name ?? ''
  sku.value = product?.sku ?? ''
  category.value = product?.category ?? ''
  quantity.value = String(product?.quantity ?? 0)
  minimum.value = String(product?.minimum ?? 0)
  price.value = String(product?.price ?? '0.00')
  errorMessage.value = ''
}, { immediate: true })

function saveProduct() {
  errorMessage.value = ''
  const normalizedSku = sku.value.trim().toLocaleUpperCase('pt-BR')
  const duplicateSku = props.products.some((item) => item.sku.toLocaleUpperCase('pt-BR') === normalizedSku && item.id !== props.product?.id)

  if (!name.value.trim() || !normalizedSku || !category.value) {
    errorMessage.value = 'Preencha todos os campos obrigatórios.'
    return
  }
  if (duplicateSku) {
    errorMessage.value = 'Este código SKU já está cadastrado. Informe outro código.'
    return
  }
  if (!Number.isInteger(Number(quantity.value)) || Number(quantity.value) < 0 || !Number.isInteger(Number(minimum.value)) || Number(minimum.value) < 0) {
    errorMessage.value = 'As quantidades devem ser números inteiros iguais ou maiores que zero.'
    return
  }
  if (!Number.isFinite(Number(price.value)) || Number(price.value) < 0) {
    errorMessage.value = 'O preço deve ser um valor igual ou maior que zero.'
    return
  }

  emit('save-product', {
    name: name.value.trim(),
    sku: normalizedSku,
    category: category.value,
    quantity: Number(quantity.value),
    minimum: Number(minimum.value),
    price: Number(price.value),
  })
}
</script>

<template>
  <div class="modal-backdrop" @click.self="emit('cancel')">
    <section class="product-modal" role="dialog" aria-modal="true" aria-labelledby="form-title">
      <div class="modal-heading">
        <div><span class="modal-eyebrow">CATÁLOGO DE PRODUTOS</span><h2 id="form-title">{{ product ? 'Editar produto' : 'Cadastrar produto' }}</h2><p>Preencha os dados para manter seu estoque organizado.</p></div>
        <button class="icon-button close-button" type="button" aria-label="Fechar formulário" @click="emit('cancel')"><svg viewBox="0 0 20 20" fill="none" aria-hidden="true"><path d="m5 5 10 10M15 5 5 15" stroke="currentColor" stroke-width="1.7" stroke-linecap="round"/></svg></button>
      </div>
      <form class="product-form" @submit.prevent="saveProduct">
        <label class="form-field form-field-wide"><span>Nome do produto <b>*</b></span><input v-model="name" type="text" maxlength="80" placeholder="Ex.: Mouse sem fio" required /></label>
        <label class="form-field"><span>Código SKU <b>*</b></span><input v-model="sku" type="text" maxlength="24" placeholder="Ex.: MOU-001" required /></label>
        <label class="form-field"><span>Categoria <b>*</b></span><select v-model="category" required><option disabled value="">Selecione uma categoria</option><option v-for="item in categories" :key="item" :value="item">{{ item }}</option></select></label>
        <label class="form-field"><span>Quantidade atual <b>*</b></span><input v-model="quantity" type="number" min="0" step="1" required /></label>
        <label class="form-field"><span>Quantidade mínima <b>*</b></span><input v-model="minimum" type="number" min="0" step="1" required /></label>
        <label class="form-field form-field-wide"><span>Preço unitário <b>*</b></span><div class="currency-input"><span>R$</span><input v-model="price" type="number" min="0" step="0.01" placeholder="0,00" required /></div></label>
        <p v-if="errorMessage" class="form-error" role="alert">{{ errorMessage }}</p>
        <div class="form-actions"><button class="button button-secondary" type="button" @click="emit('cancel')">Cancelar</button><button class="button button-primary" type="submit">{{ product ? 'Salvar alterações' : 'Salvar produto' }}</button></div>
      </form>
    </section>
  </div>
</template>