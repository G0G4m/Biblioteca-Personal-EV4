<script setup>
import { ref } from 'vue';
import BookSearch from './components/BookSearch.vue';
import MyLibrary from './components/MyLibrary.vue';

const vistaActual = ref('search'); 

const cambiarVista = (vista) => {
  vistaActual.value = vista;
};
</script>

<template>
  <div id="app">
    <header class="app-header">
      <div class="header-content">
        <h1>LetterBooks</h1>
      </div>
      <nav class="app-nav">
        <button 
          @click="cambiarVista('search')" 
          :class="{ 'nav-button': true, 'active-btn': vistaActual === 'search' }"
        >
          Buscar Libros
        </button>
        <button 
          @click="cambiarVista('library')" 
          :class="{ 'nav-button': true, 'active-btn': vistaActual === 'library' }"
        >
          Mi Biblioteca
        </button>
      </nav>
    </header>

    <main class="app-main">
      <BookSearch v-if="vistaActual === 'search'" />
      <MyLibrary v-else-if="vistaActual === 'library'" />
    </main>

    </div>
</template>

<style>
@import './assets/main.css';
</style>