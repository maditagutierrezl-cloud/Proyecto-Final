<template>
  <div class="main-layout">
    <header class="header">
      <h1>Veterinaria "MGL" - Sistema de Gestión</h1>
    </header>

    <main class="content">
      <router-view />
    </main>
  </div>
</template>

<style>
/* Estilos Globales para la Aplicación */
body {
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background-color: #f4f7f6;
  margin: 0;
  color: #333;
}

.main-layout {
  max-width: 1100px;
  margin: 0 auto;
  padding: 20px;
}

.header {
  background-color: #2c3e50;
  color: white;
  padding: 20px;
  border-radius: 8px;
  text-align: center;
  margin-bottom: 25px;
}

.form-card {
  background: white;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
  margin-bottom: 20px;
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 15px;
}

.form-card h3 {
  grid-column: 1 / -1;
  margin-top: 0;
  color: #2c3e50;
}

.form-group {
  display: flex;
  flex-direction: column;
}

.form-group label {
  font-weight: 600;
  margin-bottom: 5px;
  font-size: 0.9rem;
}

.form-group input, .form-group select {
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.button-group {
  grid-column: 1 / -1;
  display: flex;
  gap: 10px;
}

.btn {
  padding: 8px 16px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-weight: bold;
}

.btn-primary { background-color: #27ae60; color: white; }
.btn-secondary { background-color: #7f8c8d; color: white; }
.btn-edit { background-color: #f39c12; color: white; margin-right: 5px; }
.btn-delete { background-color: #e74c3c; color: white; }

.data-table {
  width: 100%;
  border-collapse: collapse;
  background: white;
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
  margin-bottom: 30px;
}

.data-table th, .data-table td {
  padding: 12px;
  text-align: left;
  border-bottom: 1px solid #eee;
}

.data-table th {
  background-color: #34495e;
  color: white;
}

.badge {
  padding: 4px 8px;
  border-radius: 12px;
  font-size: 0.8rem;
  font-weight: bold;
}
.badge.pendiente { background-color: #f1c40f; color: #333; }
.badge.confirmada { background-color: #3498db; color: white; }
.badge.atendida { background-color: #2ecc71; color: white; }
.badge.cancelada { background-color: #95a5a6; color: white; }

.divider {
  margin: 40px 0;
  border: 0;
  border-top: 2px dashed #ccc;
}

.warning-text {
  grid-column: 1 / -1;
  color: #c0392b;
  font-size: 0.85rem;
}

.text-center { text-align: center; }
</style>