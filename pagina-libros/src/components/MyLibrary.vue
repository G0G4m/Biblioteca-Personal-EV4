<template>
  <section class="my-library-section">
    <h2>Biblioteca Personal</h2>
    
    <div class="filter-controls">
      <button 
        @click="filtroActual = 'Todos'" 
        :class="{ 'filter-button': true, 'active-filter': filtroActual === 'Todos' }"
      >
        Todos
      </button>
      <button 
        @click="filtroActual = 'Pendiente'" 
        :class="{ 'filter-button': true, 'active-filter': filtroActual === 'Pendiente' }"
      >
        Pendientes
      </button>
      <button 
        @click="filtroActual = 'Incompleto'" 
        :class="{ 'filter-button': true, 'active-filter': filtroActual === 'Incompleto' }"
      >
        En Lectura
      </button>
      <button 
        @click="filtroActual = 'Finalizado'" 
        :class="{ 'filter-button': true, 'active-filter': filtroActual === 'Finalizado' }"
      >
        Finalizados
      </button>
    </div>

    <div v-if="librosFiltrados.length === 0" class="empty-library-message">
      <p v-if="filtroActual === 'Todos'">Aún no has añadido ningún libro. ¡Busca y añade tus favoritos!</p>
      <p v-else>No hay libros en la categoría "{{ filtroActual }}".</p>
    </div>

    <div v-else class="library-books">
      <LibroTarjeta 
        v-for="libro in librosFiltrados" 
        :key="libro.key" 
        :libro="libro"
        :obtenerPortada="obtenerPortadaLibro"
      >
        <template #acciones>
          <div class="book-status-actions">
            <select 
              v-model="libro.status" 
              @change="cambiarEstadoLibro(libro)" 
              class="status-dropdown"
              :class="{'status-pendiente': libro.status === 'Pendiente', 'status-incompleto': libro.status === 'Incompleto', 'status-finalizado': libro.status === 'Finalizado'}"
            >
              <option value="Pendiente">Pendiente</option>
              <option value="Incompleto">En Lectura</option>
              <option value="Finalizado">Finalizado</option>
            </select>
            <button @click="eliminarLibro(libro.key)" class="boton-accion eliminar-btn">Eliminar</button>
          </div>
        </template>
      </LibroTarjeta>
    </div>
  </section>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue';
import LibroTarjeta from './LibroTarjeta.vue';

const misLibros = ref([]);
const filtroActual = ref('Todos'); 

const obtenerPortadaLibro = (idPortada) => {
  return idPortada ? `https://covers.openlibrary.org/b/id/${idPortada}-M.jpg` : 'https://via.placeholder.com/150x200?text=Sin+Portada';
};

onMounted(() => {
  cargarLibrosDesdeLocalStorage();
});

const cargarLibrosDesdeLocalStorage = () => {
  const librosAlmacenados = localStorage.getItem('misLibros');
  if (librosAlmacenados) {
    misLibros.value = JSON.parse(librosAlmacenados);
  }
};

const eliminarLibro = (claveLibro) => {
  misLibros.value = misLibros.value.filter(libro => libro.key !== claveLibro);
  localStorage.setItem('misLibros', JSON.stringify(misLibros.value));
  alert('Libro eliminado de tu biblioteca.');
};

const cambiarEstadoLibro = (libro) => {
  localStorage.setItem('misLibros', JSON.stringify(misLibros.value));
};

const librosFiltrados = computed(() => {
  if (filtroActual.value === 'Todos') {
    return misLibros.value;
  }
  return misLibros.value.filter(libro => libro.status === filtroActual.value);
});
</script>

<style scoped>
.my-library-section {
  padding: 32px 16px;
  max-width: 1100px;
  margin: 32px auto;
  background: var(--color-background-soft); 
  border-radius: 18px;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.10);
  backdrop-filter: blur(2px);
  border: 1px solid var(--color-border-medium);
}

h2 {
  color: var(--color-primary); 
  margin-bottom: 32px;
  font-size: 2.3em;
  font-weight: 700;
  letter-spacing: 1px;
  text-align: center;
}

.filter-controls {
  display: flex;
  justify-content: center;
  gap: 15px;
  margin-bottom: 35px;
  flex-wrap: wrap;
}

.filter-button {
  padding: 12px 25px;
  background-color: var(--color-background-light);
  color: var(--color-text-dark);
  border: 1px solid var(--color-border-medium);
  border-radius: 8px;
  cursor: pointer;
  font-size: 1em;
  font-weight: 600;
  transition: background-color 0.3s ease, border-color 0.3s ease, transform 0.2s ease;
  box-shadow: 0 2px 8px rgba(120, 72, 36, 0.05);
}

.filter-button:hover {
  background-color: var(--color-background-soft);
  border-color: var(--color-secondary);
  transform: translateY(-2px);
}

.filter-button.active-filter {
  background-color: var(--color-secondary);
  color: var(--color-card-background);
  border-color: var(--color-secondary);
  box-shadow: 0 4px 10px rgba(120, 72, 36, 0.15);
  transform: translateY(-1px);
}

.empty-library-message {
  text-align: center;
  font-size: 1.15em;
  color: var(--color-text-dark); 
  padding: 40px 16px;
  border: 2px dashed var(--color-border-medium);
  border-radius: 12px;
  background: var(--color-background-light);
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.04);
}

.library-books {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
  gap: 32px;
  justify-content: center;
  margin-bottom: 24px;
}

.book-status-actions {
  display: flex;
  flex-direction: column; 
  gap: 10px;
  margin-top: 15px;
  width: 100%; 
}

.status-dropdown {
  padding: 10px 15px;
  border: 1px solid var(--color-border-medium);
  border-radius: 8px;
  background-color: var(--color-background-light); 
  color: var(--color-text-dark); 
  font-size: 1em;
  cursor: pointer;
  appearance: none; 
  background-image: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24"><path fill="rgb(47, 79, 79)" d="M7 10l5 5 5-5z"/></svg>'); /* Icono de flecha personalizado, ahora en el color del texto oscuro */
  background-repeat: no-repeat;
  background-position: right 10px center;
  background-size: 12px;
  box-shadow: 0 2px 5px rgba(0,0,0,0.05);
  transition: border-color 0.2s;
}
.status-dropdown option {
    color: var(--color-text-dark); 
    background-color: var(--color-background-light); 
}
.status-dropdown:focus {
  outline: none;
  border-color: var(--color-primary);
}


.status-dropdown.status-pendiente {
  border-color: var(--color-primary); 
}
.status-dropdown.status-incompleto {
  border-color: var(--color-secondary); 
}
.status-dropdown.status-finalizado {
  border-color: var(--color-success); 
}


.boton-accion {
  padding: 10px 22px;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-size: 1em;
  font-weight: 700;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.08);
  transition: background 0.3s, transform 0.2s;
  outline: none;
  width: 100%; 
}
.boton-accion:focus {
  box-shadow: 0 0 0 2px rgba(0, 0, 0, 0.1);
}

.eliminar-btn {
  background: linear-gradient(90deg, var(--color-error) 0%, #F5A8A8 100%); 
  color: var(--color-text-dark); /* Texto oscuro para el botón eliminar */
}
.eliminar-btn:hover {
  background: linear-gradient(90deg, #F5A8A8 0%, var(--color-error) 100%);
  transform: scale(1.05);
}

@media (max-width: 700px) {
  .my-library-section {
    padding: 16px 4px;
    margin: 12px auto;
  }
  .library-books {
    gap: 18px;
  }
  .filter-controls {
    gap: 10px;
    justify-content: center;
  }
  .filter-button {
    padding: 10px 18px;
    font-size: 0.9em;
  }
}
</style>