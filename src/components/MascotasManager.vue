<script setup>
import { ref, computed } from 'vue'

const props = defineProps({
  mascotas: Array
})

const emit = defineEmits(['agregar', 'editar', 'eliminar'])

// Estado de Búsqueda y Filtro por Especie (Categoría)
const busqueda = ref('')
const filtroEspecie = ref('Todas')

// Estado del formulario
const idEditando = ref(null)
const formulario = ref({
  nombre: '',
  especie: 'Perro',
  edad: 1,
  propietario: ''
})

// Propiedad computada que aplica tanto el buscador por nombre/dueño como el filtro por especie
const mascotasFiltradas = computed(() => {
  return props.mascotas.filter(m => {
    const termino = busqueda.value.toLowerCase()
    const coincideTexto = m.nombre.toLowerCase().includes(termino) ||
                           m.propietario.toLowerCase().includes(termino)
    
    const coincideEspecie = filtroEspecie.value === 'Todas' || m.especie === filtroEspecie.value

    return coincideTexto && coincideEspecie
  })
})

const resetearFormulario = () => {
  idEditando.value = null
  formulario.value = { nombre: '', especie: 'Perro', edad: 1, propietario: '' }
}

const guardarMascota = () => {
  if (!formulario.value.nombre || !formulario.value.propietario) {
    alert('Por favor complete todos los campos obligatorios.')
    return
  }

  if (idEditando.value) {
    emit('editar', { id: idEditando.value, ...formulario.value })
  } else {
    emit('agregar', { ...formulario.value })
  }
  resetearFormulario()
}

const seleccionarParaEditar = (mascota) => {
  idEditando.value = mascota.id
  formulario.value = { ...mascota }
}
</script>

<template>
  <div class="crud-section">
    <h2>1. Gestión de Mascotas</h2>

    <!-- Formulario Crear / Editar Mascotas -->
    <form @submit.prevent="guardarMascota" class="form-card">
      <h3>{{ idEditando ? 'Editar Mascota' : 'Registrar Nueva Mascota' }}</h3>
      
      <div class="form-group">
        <label>Nombre:</label>
        <input v-model="formulario.nombre" type="text" placeholder="Ej: Luna" required />
      </div>

      <div class="form-group">
        <label>Especie:</label>
        <select v-model="formulario.especie">
          <option value="Perro">Perro</option>
          <option value="Gato">Gato</option>
          <option value="Ave">Ave</option>
          <option value="Otro">Otro</option>
        </select>
      </div>

      <div class="form-group">
        <label>Edad (años):</label>
        <input v-model.number="formulario.edad" type="number" min="0" required />
      </div>

      <div class="form-group">
        <label>Propietario:</label>
        <input v-model="formulario.propietario" type="text" placeholder="Ej: Carlos Pérez" required />
      </div>

      <div class="button-group">
        <button type="submit" class="btn btn-primary">
          {{ idEditando ? 'Actualizar Mascota' : 'Guardar Mascota' }}
        </button>
        <button v-if="idEditando" type="button" @click="resetearFormulario" class="btn btn-secondary">
          Cancelar
        </button>
      </div>
    </form>

    <!-- Buscador y Filtro por Especie (Categoría) -->
    <div class="search-box">
      <div class="search-item">
        <label>🔍 <strong>Buscar Mascota:</strong></label>
        <input 
          v-model="busqueda" 
          type="text" 
          placeholder="Nombre o propietario..." 
          class="search-input"
        />
      </div>

      <div class="search-item">
        <label>🐱 <strong>Filtrar por Especie:</strong></label>
        <select v-model="filtroEspecie" class="search-select">
          <option value="Todas">Todas</option>
          <option value="Perro">Perro</option>
          <option value="Gato">Gato</option>
          <option value="Ave">Ave</option>
          <option value="Otro">Otro</option>
        </select>
      </div>
    </div>

    <!-- Tabla de Mascotas -->
    <table class="data-table">
      <thead>
        <tr>
          <th>ID</th>
          <th>Nombre</th>
          <th>Especie</th>
          <th>Edad</th>
          <th>Propietario</th>
          <th>Acciones</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="m in mascotasFiltradas" :key="m.id">
          <td>#{{ m.id }}</td>
          <td><strong>{{ m.nombre }}</strong></td>
          <td>{{ m.especie }}</td>
          <td>{{ m.edad }} años</td>
          <td>{{ m.propietario }}</td>
          <td>
            <button @click="seleccionarParaEditar(m)" class="btn btn-edit">Editar</button>
            <button @click="emit('eliminar', m.id)" class="btn btn-delete">Eliminar</button>
          </td>
        </tr>
        <tr v-if="mascotasFiltradas.length === 0">
          <td colspan="6" class="text-center">No se encontraron mascotas con el filtro seleccionado.</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<style scoped>
.search-box {
  background: white;
  padding: 15px;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.05);
  margin-bottom: 15px;
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
}

.search-item {
  display: flex;
  align-items: center;
  gap: 8px;
  flex: 1;
  min-width: 220px;
}

.search-input, .search-select {
  padding: 8px 12px;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 0.95rem;
  width: 100%;
}
</style>