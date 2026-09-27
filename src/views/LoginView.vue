<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router' 

const router = useRouter()
const usuario = ref('')
const password = ref('')
const error = ref('')

const iniciarSesion = () => {
  // Simulación de validación de credenciales
  if (usuario.value === 'admin' && password.value === '1234') {
    // Guardar estado de autenticación en localStorage
    localStorage.setItem('isAuthenticated', 'true')
    localStorage.setItem('user', usuario.value)
    error.value = ''
    router.push('/dashboard')
  } else {
    error.value = 'Usuario o contraseña incorrectos. (Usa: admin / 1234)'
  }
}
</script>

<template>
  <div class="login-container">
    <div class="login-card">
      <h2>Iniciar Sesión</h2>
      <p class="subtitle">Acceso al Sistema de Gestión Veterinaria</p>

      <form @submit.prevent="iniciarSesion">
        <div class="form-group">
          <label>Usuario:</label>
          <input v-model="usuario" type="text" placeholder="Ej: admin" required />
        </div>

        <div class="form-group">
          <label>Contraseña:</label>
          <input v-model="password" type="password" placeholder="Ej: 1234" required />
        </div>

        <p v-if="error" class="error-msg">{{ error }}</p>

        <button type="submit" class="btn btn-primary btn-block">Ingresar</button>
      </form>
    </div>
  </div>
</template>

<style scoped>
.login-container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 80vh;
}

.login-card {
  background: white;
  padding: 30px;
  border-radius: 8px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
  width: 100%;
  max-width: 380px;
}

h2 {
  margin-top: 0;
  color: #2c3e50;
  text-align: center;
}

.subtitle {
  text-align: center;
  color: #7f8c8d;
  font-size: 0.9rem;
  margin-bottom: 20px;
}

.form-group {
  margin-bottom: 15px;
  display: flex;
  flex-direction: column;
}

.form-group label {
  font-weight: 600;
  margin-bottom: 5px;
  font-size: 0.9rem;
}

.form-group input {
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.error-msg {
  color: #e74c3c;
  font-size: 0.85rem;
  margin-bottom: 15px;
  text-align: center;
}

.btn-block {
  width: 100%;
  padding: 10px;
}
</style>