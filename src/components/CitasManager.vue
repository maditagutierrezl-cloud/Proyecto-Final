<script setup>
import { ref, computed } from 'vue'

const props = defineProps({
  citas: Array,
  mascotas: Array
})

const emit = defineEmits(['agregar', 'editar', 'eliminar'])

// Estados de Búsqueda y Filtro
const busqueda = ref('')
const filtroEstado = ref('Todos')

const idEditando = ref(null)
const formulario = ref({
  fecha: '',
  motivo: '',
  estado: 'Pendiente',
  mascotaId: ''
})

const obtenerMascota = (mascotaId) => {
  return props.mascotas.find(m => m.id === mascotaId) || { nombre: 'Mascota no encontrada', propietario: 'N/A' }
}

// Propiedad computada para filtrar citas por término de búsqueda y por estado
const citasFiltradas = computed(() => {
  return props.citas.filter(c => {
    const mascota = obtenerMascota(c.mascotaId)
    const termino = busqueda.value.toLowerCase()
    
    // Coincidencia por motivo o por nombre de mascota
    const coincideTexto = c.motivo.toLowerCase().includes(termino) || 
                           mascota.nombre.toLowerCase().includes(termino)
    
    // Coincidencia por estado seleccionado
    const coincideEstado = filtroEstado.value === 'Todos' || c.estado === filtroEstado.value

    return coincideTexto && coincideEstado
  })
})

const resetearFormulario = () => {
  idEditando.value = null
  formulario.value = { fecha: '', motivo: '', estado: 'Pendiente', mascotaId: '' }
}

const guardarCita = () => {
  if (!formulario.value.fecha || !formulario.value.motivo || !formulario.value.mascotaId) {
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

const seleccionarParaEditar = (cita) => {
  idEditando.value = cita.id
  formulario.value = { ...cita }
}
</script>

<template>
  <div class="crud-section">
    <h2>2. Gestión de Citas Médicas</h2>

    <!-- Formulario Crear / Editar Citas -->
    <form @submit.prevent="guardarCita" class="form-card">
      <h3>{{ idEditando ? 'Editar Cita' : 'Programar Nueva Cita' }}</h3>

      <div class="form-group">
        <label>Mascota Asociada:</label>
        <select v-model.number="formulario.mascotaId" required>
          <option value="" disabled>-- Seleccione una mascota --</option>
          <option v-for="m in mascotas" :key="m.id" :value="m.id">
            {{ m.nombre }} (Dueño: {{ m.propietario }})
          </option>
        </select>
      </div>

      <div class="form-group">
        <label>Fecha y Hora:</label>
        <input v-model="formulario.fecha" type="datetime-local" required />
      </div>

      <div class="form-group">
        <label>Motivo de la consulta:</label>
        <input v-model="formulario.motivo" type="text" placeholder="Ej: Vacunación anual" required />
      </div>

      <div class="form-group">
        <label>Estado:</label>
        <select v-model="formulario.estado">
          <option value="Pendiente">Pendiente</option>
          <option value="Confirmada">Confirmada</option>
          <option value="Atendida">Atendida</option>
          <option value="Cancelada">Cancelada</option>
        </select>
      </div>

      <div class="button-group">
        <button type="submit" class="btn btn-primary" :disabled="mascotas.length === 0">
          {{ idEditando ? 'Actualizar Cita' : 'Programar Cita' }}
        </button>
        <button v-if="idEditando" type="button" @click="resetearFormulario" class="btn btn-secondary">
          Cancelar
        </button>
      </div>
      <p v-if="mascotas.length === 0" class="warning-text">
        * Debe registrar al menos una mascota antes de poder programar citas.
      </p>
    </form>

    <!-- Barra de Búsqueda y Filtros -->
    <div class="search-box">
      <div class="search-item">
        <label>🔍 <strong>Buscar Cita:</strong></label>
        <input 
          v-model="busqueda" 
          type="text" 
          placeholder="Buscar por motivo o nombre de mascota..." 
          class="search-input"
        />
      </div>

      <div class="search-item">
        <label>📌 <strong>Filtrar por Estado:</strong></label>
        <select v-model="filtroEstado" class="search-select">
          <option value="Todos">Todos</option>
          <option value="Pendiente">Pendiente</option>
          <option value="Confirmada">Confirmada</option>
          <option value="Atendida">Atendida</option>
          <option value="Cancelada">Cancelada</option>
        </select>
      </div>
    </div>

    <!-- Tabla de Citas -->
    <table class="data-table">
      <thead>
        <tr>
          <th>ID</th>
          <th>Mascota</th>
          <th>Dueño</th>
          <th>Fecha y Hora</th>
          <th>Motivo</th>
          <th>Estado</th>
          <th>Acciones</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="c in citasFiltradas" :key="c.id">
          <td>#{{ c.id }}</td>
          <td><strong>{{ obtenerMascota(c.mascotaId).nombre }}</strong></td>
          <td>{{ obtenerMascota(c.mascotaId).propietario }}</td>
          <td>{{ new Date(c.fecha).toLocaleString() }}</td>
          <td>{{ c.motivo }}</td>
          <td>
            <span :class="['badge', c.estado.toLowerCase()]">{{ c.estado }}</span>
          </td>
          <td>
            <button @click="seleccionarParaEditar(c)" class="btn btn-edit">Editar</button>
            <button @click="emit('eliminar', c.id)" class="btn btn-delete">Eliminar</button>
          </td>
        </tr>
        <tr v-if="citasFiltradas.length === 0">
          <td colspan="7" class="text-center">No se encontraron citas que coincidan con la búsqueda.</td>
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
  min-width: 250px;
}

.search-input, .search-select {
  padding: 8px 12px;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 0.95rem;
  width: 100%;
}
</style>