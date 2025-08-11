
<template>
  
  <div class="container">
    <!-- Coluna da esquerda: Categorias -->
    <aside class="sidebar">
      <h2>Categorias</h2>
      <ul>
        <li
          v-for="categoria in categoriasUnicas"
          :key="categoria"
          @click="filtrarPorCategoria(categoria)"
          :class="{ ativo: categoria === categoriaSelecionada }"
        >
          {{ categoria }}
        </li>
      </ul>
    </aside>

    <!-- Coluna da direita: Subcategorias + Pontos -->
    <main class="conteudo">
      <h1>Pontos de Umbanda</h1>

      <!-- Subcategorias -->
<div v-if="subcategoriasFiltradas.length" class="subcategorias">
  <h3>Subcategorias</h3>
  <ul>
    <li
      v-for="sub in subcategoriasFiltradas"
      :key="sub"
      @click="filtrarPorSubcategoria(sub)"
      :class="{ ativo: sub === subcategoriaSelecionada }"
    >
      {{ sub }}
    </li>
  </ul>
</div>

      <!-- Pontos -->
      <div v-for="ponto in pontosFiltrados" :key="ponto.sequencia" class="card">
        <h3>{{ ponto.titulo }}</h3>
        <p>{{ ponto.letra }}</p>
        <a :href="ponto.link" target="_blank">🎵 Ouvir ponto</a>
        <p><strong>Categorias:</strong> {{ ponto.categorias.join(', ') }}</p>
        <p><strong>Subcategorias:</strong> {{ ponto.subcategorias.join(', ') }}</p>
      </div>
    </main>
  </div>


</template>


<script setup>
import { ref, onMounted, computed } from 'vue'

const pontos = ref([])
const categoriaSelecionada = ref(null)
const subcategoriaSelecionada = ref(null)

onMounted(async () => {
  const res = await fetch('/src/assets/pontos.json')
  pontos.value = await res.json()
})

const categoriasUnicas = computed(() => {
  const todas = pontos.value.flatMap(p => p.categorias)
  return [...new Set(todas)]
})

const pontosDaCategoria = computed(() => {
  if (!categoriaSelecionada.value) return []
  return pontos.value.filter(p =>
    p.categorias.includes(categoriaSelecionada.value)
  )
})

const subcategoriasFiltradas = computed(() => {
  const todas = pontosDaCategoria.value.flatMap(p => p.subcategorias)
  return [...new Set(todas)]
})

const pontosFiltrados = computed(() => {
  if (!subcategoriaSelecionada.value) return pontosDaCategoria.value
  return pontosDaCategoria.value.filter(p =>
    p.subcategorias.includes(subcategoriaSelecionada.value)
  )
})

function filtrarPorCategoria(categoria) {
  categoriaSelecionada.value = categoria
  subcategoriaSelecionada.value = null // resetar subcategoria ao trocar categoria
}

function filtrarPorSubcategoria(sub) {
  subcategoriaSelecionada.value =
    subcategoriaSelecionada.value === sub ? null : sub
}
</script>


<!-- <style src="../assets/estilos.css"></style> -->
