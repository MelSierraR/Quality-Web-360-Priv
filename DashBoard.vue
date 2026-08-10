<template>
  <div class="container">

    <!-- NAVBAR -->
    <Navbar />

    <!-- SIDEBAR -->
    <Sidebar
      :vista-actual="vistaActual"
      @navigate="cambiarVista"
    />

    <!-- CONTENIDO -->
    <main class="content">

      <!-- INICIO -->
      <template v-if="vistaActual === 'inicio'">
        <h1>Bienvenido</h1>
        <p>Aquí irá todo el contenido del sistema.</p>
      </template>

      <!-- OTRAS VISTAS -->
      <component
        v-else-if="componenteActual"
        :is="componenteActual"
      />

    </main>

  </div>
</template>

<script setup>
import { ref, computed } from "vue";

import Navbar from "./NavBar.vue";
import Sidebar from "./Sidebar.vue";
import ListaUsu from "./ListaUsu.vue";

import "./DashBoard.css";


/* =========================
   VISTA ACTUAL
========================= */

const vistaActual = ref("inicio");


/* =========================
   COMPONENTES
========================= */

const componentes = {
  usuarios: ListaUsu,
};


/* =========================
   COMPONENTE ACTUAL
========================= */

const componenteActual = computed(() => {
  return componentes[vistaActual.value] || null;
});


/* =========================
   NAVEGACIÓN
========================= */

const cambiarVista = (vista) => {
  vistaActual.value = vista;
};
</script>