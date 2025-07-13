<template>
  <div class="libro-tarjeta">
    <img 
      :src="obtenerPortada(libro.cover_i)" 
      :alt="`Portada de ${libro.title}`" 
      class="libro-portada"
    >
    <div class="libro-info">
      <h3 class="libro-titulo">{{ libro.title }}</h3>
      <p class="libro-autor">{{ libro.author_name || 'Desconocido' }}</p>
      <p class="libro-anio">Año: {{ libro.first_publish_year || 'N/A' }}</p>
      
      <slot name="acciones"></slot> 
    </div>
  </div>
</template>

<script setup>
import { defineProps } from 'vue';

const props = defineProps({
  libro: {
    type: Object,
    required: true
  },
  obtenerPortada: {
    type: Function,
    required: true
  }
});
</script>

<style scoped>
.libro-tarjeta {
  background: linear-gradient(135deg, var(--color-card-background) 0%, var(--color-background-soft) 100%);
  border: 1px solid var(--color-border-medium);
  border-radius: 10px;
  padding: 20px;
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
  box-shadow: 0 4px 14px rgba(120, 72, 36, 0.12);
  transition: transform 0.3s ease, box-shadow 0.3s ease;
  overflow: hidden;
}
.libro-tarjeta:hover {
  transform: translateY(-7px);
  box-shadow: 0 8px 22px rgba(120, 72, 36, 0.18);
}
.libro-portada {
  width: 160px;
  height: 220px;
  object-fit: cover;
  border-radius: 6px;
  margin-bottom: 15px;
  box-shadow: 0 3px 8px rgba(120, 72, 36, 0.13);
}
.libro-info {
  flex-grow: 1;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  width: 100%;
}
.libro-titulo {
  font-size: 1.3em;
  color: var(--color-primary); 
  margin-bottom: 8px;
  line-height: 1.4;
  min-height: 2.8em;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  overflow: hidden;
  text-overflow: ellipsis;
  word-break: break-word;
}
.libro-autor {
  font-size: 1em;
  color: var(--color-secondary); 
  margin-bottom: 5px;
}
.libro-anio {
  font-size: 0.95em;
  color: #7C5A3A;
  margin-bottom: 15px;
}
</style>