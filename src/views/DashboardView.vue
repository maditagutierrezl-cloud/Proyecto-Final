<script setup lang="ts">
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import MascotasManager from '../components/MascotasManager.vue'
import CitasManager from '../components/CitasManager.vue'

const router = useRouter()
const API_URL = import.meta.env.VITE_API_URL || 'http://localhost:3000'

const mascotas = ref<any[]>([])
const citas = ref<any[]>([])
const usuarioActual = ref<string>(localStorage.getItem('user') || 'Usuario')

const cargarDatos = async () => {
  try {
    const resMascotas = await fetch(`${API_URL}/mascotas`)
    mascotas.value = await resMascotas.json()

    const resCitas = await fetch(`${API_URL}/citas`)
    citas.value = await resCitas.json()
  } catch (error) {
    console.error('Error al cargar datos desde la API:', error)
  }
}

onMounted(() => {
  cargarDatos()
})

// Operaciones CRUD Mascotas
const agregarMascota = async (nuevaMascota: any) => {
  try {
    const res = await fetch(`${API_URL}/mascotas`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(nuevaMascota)
    })
    const creada = await res.json()
    mascotas.value.push(creada)
  } catch (error) {
    console.error('Error al agregar mascota:', error)
  }
}

const editarMascota = async (mascotaActualizada: any) => {
  try {
    const res = await fetch(`${API_URL}/mascotas/${mascotaActualizada.id}`, {
      method: 'PUT',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(mascotaActualizada)
    })
    const editada = await res.json()
    const index = mascotas.value.findIndex(m => m.id === editada.id)
    if (index !== -1) mascotas.value[index] = editada
  } catch (error) {
    console.error('Error al editar mascota:', error)
  }
}

const eliminarMascota = async (id: any) => {
  if (!confirm('¿Está seguro de eliminar esta mascota? Se eliminarán también sus citas asociadas.')) return
  try {
    await fetch(`${API_URL}/mascotas/${id}`, { method: 'DELETE' })
    mascotas.value = mascotas.value.filter(m => String(m.id) !== String(id))

    const citasAEliminar = citas.value.filter(c => String(c.mascotaId) === String(id))
    for (const c of citasAEliminar) {
      await fetch(`${API_URL}/citas/${c.id}`, { method: 'DELETE' })
    }
    citas.value = citas.value.filter(c => String(c.mascotaId) !== String(id))
  } catch (error) {
    console.error('Error al eliminar mascota:', error)
  }
}

// Operaciones CRUD Citas
const agregarCita = async (nuevaCita: any) => {
  try {
    const res = await fetch(`${API_URL}/citas`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(nuevaCita)
    })
    const creada = await res.json()
    citas.value.push(creada)
  } catch (error) {
    console.error('Error al programar cita:', error)
  }
}

const editarCita = async (citaActualizada: any) => {
  try {
    const res = await fetch(`${API_URL}/citas/${citaActualizada.id}`, {
      method: 'PUT',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(citaActualizada)
    })
    const editada = await res.json()
    const index = citas.value.findIndex(c => c.id === editada.id)
    if (index !== -1) citas.value[index] = editada
  } catch (error) {
    console.error('Error al editar cita:', error)
  }
}

const eliminarCita = async (id: any) => {
  if (!confirm('¿Está seguro de cancelar/eliminar esta cita médica?')) return
  try {
    await fetch(`${API_URL}/citas/${id}`, { method: 'DELETE' })
    citas.value = citas.value.filter(c => String(c.id) !== String(id))
  } catch (error) {
    console.error('Error al eliminar cita:', error)
  }
}

const cerrarSesion = () => {
  localStorage.removeItem('isAuthenticated')
  localStorage.removeItem('user')
  router.push('/login')
}
</script>

<template>
  <div>
    <div class="user-bar">
      <span>Bienvenido, <strong>{{ usuarioActual }}</strong></span>
      <button @click="cerrarSesion" class="btn btn-delete">Cerrar Sesión</button>
    </div>

    <MascotasManager 
      :mascotas="mascotas"
      @agregar="agregarMascota"
      @editar="editarMascota"
      @eliminar="eliminarMascota"
    />

    <hr class="divider" />

    <CitasManager 
      :citas="citas"
      :mascotas="mascotas"
      @agregar="agregarCita"
      @editar="editarCita"
      @eliminar="eliminarCita"
    />
  </div>
</template>

<style scoped>
.user-bar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background: white;
  padding: 12px 20px;
  border-radius: 8px;
  margin-bottom: 20px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.05);
}
</style>