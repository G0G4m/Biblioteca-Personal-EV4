<template>
  <section class="book-search-section">
    <h2>¡Busca tu proximo libro!</h2>
    <div class="search-input-group">
      <input 
        type="text" 
        v-model="terminoBusqueda" 
        @keyup.enter="buscarLibros" 
        placeholder="Título o autor..."
        class="search-input"
      >
      <button @click="buscarLibros" class="search-button">Buscar</button>
    </div>

    <div v-if="cargando" class="loading-message">Buscando libros...</div>
    <div v-if="error" class="error-message">{{ error }}</div>

    <div v-if="librosEncontrados.length > 0" class="book-results">
      <LibroTarjeta 
        v-for="libro in librosEncontrados" 
        :key="libro.key" 
        :libro="libro"
        :obtenerPortada="obtenerPortadaLibro"
      >
        <template #acciones>
          <button @click="guardarLibro(libro)" class="boton-accion guardar-btn">Añadir a mi biblioteca</button>
        </template>
      </LibroTarjeta>
    </div>
    <div v-else-if="!cargando && !error && terminoBusqueda && librosEncontrados.length === 0" class="no-results-message">
      No se encontraron resultados para "{{ terminoBusqueda }}".
    </div>
  </section>
</template>

<script setup>
import { ref } from 'vue';
import LibroTarjeta from './LibroTarjeta.vue';

const terminoBusqueda = ref('');
const librosEncontrados = ref([]);
const cargando = ref(false);
const error = ref(null);

const obtenerPortadaLibro = (idPortada) => {
  return idPortada ? `https://covers.openlibrary.org/b/id/${idPortada}-M.jpg` : 'https://via.placeholder.com/150x200?text=Sin+Portada';
};

const buscarLibros = async () => {
  if (!terminoBusqueda.value.trim()) {
    error.value = 'Por favor, ingresa un término de búsqueda.';
    librosEncontrados.value = [];
    return;
  }

  cargando.value = true;
  error.value = null;
  librosEncontrados.value = [];

  try {
    const response = await fetch(`https://openlibrary.org/search.json?q=${encodeURIComponent(terminoBusqueda.value)}&limit=10`);
    if (!response.ok) {
      throw new Error(`Error HTTP: ${response.status}`);
    }
    const data = await response.json();
    
    // Se filtra para asegurar que haya un titulo y un nombre de autor
    librosEncontrados.value = data.docs.filter(libro => libro.title && libro.author_name); 

    if (librosEncontrados.value.length === 0 && data.docs.length > 0) {
        error.value = 'No se encontraron libros con título y autor definidos para tu búsqueda.';
    }

  } catch (err) {
    console.error('Error al buscar libros:', err);
    error.value = 'Hubo un problema al cargar los libros. Inténtalo de nuevo más tarde.';
  } finally {
    cargando.value = false;
  }
};

const guardarLibro = (libroAGuardar) => {
  const libroSimplificado = {
    key: libroAGuardar.key,
    title: libroAGuardar.title,
    author_name: libroAGuardar.author_name ? libroAGuardar.author_name.join(', ') : 'Desconocido',
    cover_i: libroAGuardar.cover_i,
    first_publish_year: libroAGuardar.first_publish_year,
    status: 'Pendiente' 
  };

  let misLibros = JSON.parse(localStorage.getItem('misLibros')) || [];
  
  if (!misLibros.some(libro => libro.key === libroSimplificado.key)) {
    misLibros.push(libroSimplificado);
    localStorage.setItem('misLibros', JSON.stringify(misLibros));
    alert(`${libroSimplificado.title} ha sido añadido a tu lista de Pendientes.`);
  } else {
    alert(`${libroSimplificado.title} ya está en tu biblioteca.`);
  }
};
</script>

<style scoped>

  .book-search-section {
    padding: 32px 16px;
    max-width: 1100px;
    margin: 32px auto;
    background: linear-gradient(135deg, var(--color-card-background) 0%, var(--color-background-soft) 100%);
    border-radius: 18px;
    box-shadow: 0 8px 32px rgba(120, 72, 36, 0.10);
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

.search-input-group {
  display: flex;
  justify-content: center;
  gap: 12px;
  margin-bottom: 36px;
  flex-wrap: wrap;
}

  .search-input {
    padding: 14px 18px;
    border: 2px solid var(--color-border-medium);
    border-radius: 8px;
    width: 70%;
    max-width: 420px;
    font-size: 1.08em;
    color: var(--color-text-dark);
    background: var(--color-background-light);
    outline: none;
    transition: border-color 0.2s;
    box-shadow: 0 2px 8px rgba(120, 72, 36, 0.04);
  }
  .search-input:focus {
    border-color: var(--color-primary);
  }
  .search-input::placeholder {
    color: var(--color-text-medium);
    font-style: italic;
  }

  .search-button {
    padding: 14px 28px;
    background: linear-gradient(90deg, var(--color-secondary) 0%, var(--color-success) 100%);
    color: var(--color-text-dark);
    border: none;
    border-radius: 8px;
    cursor: pointer;
    font-size: 1.08em;
    font-weight: 700;
    box-shadow: 0 2px 8px rgba(120, 72, 36, 0.10);
    transition: background 0.3s, transform 0.2s;
    outline: none;
  }
  .search-button:focus {
    box-shadow: 0 0 0 2px var(--color-secondary);
  }
  .search-button:hover {
    background: linear-gradient(90deg, var(--color-success) 0%, var(--color-secondary) 100%);
    transform: scale(1.04);
  }

  .loading-message, .error-message, .no-results-message {
    text-align: center;
    margin-top: 24px;
    font-size: 1.15em;
    color: var(--color-text-dark);
    padding: 10px 0;
    border-radius: 6px;
  }
  .error-message {
    color: var(--color-error);
    background: var(--color-background-soft);
    border: 1px solid var(--color-error);
  }
  .loading-message {
    background: var(--color-background-light);
    border: 1px solid var(--color-secondary);
  }
  .no-results-message {
    background: var(--color-card-background);
    border: 1px solid var(--color-border-medium);
  }

.book-results {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
  gap: 32px;
  justify-content: center;
  margin-bottom: 24px;
}

  .boton-accion {
    padding: 10px 22px;
    border: none;
    border-radius: 8px;
    cursor: pointer;
    font-size: 1em;
    font-weight: 700;
    box-shadow: 0 2px 8px rgba(120, 72, 36, 0.08);
    transition: background 0.3s, transform 0.2s;
    outline: none;
    margin-top: auto;
  }
  .boton-accion:focus {
    box-shadow: 0 0 0 2px var(--color-secondary);
  }

  .guardar-btn {
    background: linear-gradient(90deg, var(--color-success) 0%, var(--color-secondary) 100%);
    color: var(--color-text-dark);
  }
  .guardar-btn:hover {
    background: linear-gradient(90deg, var(--color-secondary) 0%, var(--color-success) 100%);
    transform: scale(1.05);
  }

  @media (max-width: 700px) {
    .book-search-section {
      padding: 16px 4px;
      margin: 12px auto;
      background: linear-gradient(135deg, var(--color-card-background) 0%, var(--color-background-soft) 100%);
    }
    .search-input {
      width: 100%;
      max-width: 100%;
      background: var(--color-background-light);
      color: var(--color-text-dark);
    }
    .book-results {
      gap: 18px;
    }
  }
</style>