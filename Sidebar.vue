```vue
<template>
  <aside class="sidebar">

    <div class="menu-group">

      <div
        class="menu-item"
        @click="irA('inicio')"
      >
        <Home :size="18" />
        Inicio
      </div>

      <div class="menu-item">
        <Search :size="18" />
        Visor Documental
      </div>

      <div class="menu-item">
        <Archive :size="18" />
        Almacén de Registros
      </div>

    </div>

    <br />
    <br />

    <div class="menu-title">
      MÓDULOS
    </div>

    <div class="menu-group">

      <button
        class="menu-button"
        @click="catalogos = !catalogos"
      >
        <LayoutGrid :size="18" />
        Control General ▼
      </button>

      <div
        v-if="catalogos"
        class="submenu"
      >

        <div
          :class="[
            'submenu-item',
            vistaActual === 'usuarios'
              ? 'submenu-item--activo'
              : ''
          ]"
          @click="irA('usuarios')"
        >
          <Users :size="16" />
          Catalago de Usuarios
        </div>

        <div class="submenu-item">
          <Workflow :size="16" />
          Catalago de Procesos
        </div>

        <div class="submenu-item">
          <ClipboardCheck :size="16" />
          Catalago de Auditores
        </div>

        <div class="submenu-item">
          <BarChart3 :size="16" />
          Catalago de Indicadores
        </div>

        <div class="submenu-item">
          <Copy :size="16" />
          Copias Controladas
        </div>

      </div>

      <div class="menu-item">
        <FolderCheck :size="18" />
        Control de Documentos
      </div>

      <div class="submenu-item">
        <FileType :size="16" />
        Tipo de Documentos
      </div>

      <div class="menu-item">
        <RefreshCcw :size="18" />
        Solicitudes de Cambios y Acciones
      </div>

      <div class="menu-item">
        <Wrench :size="18" />
        Acciones Correctivas
      </div>

      <div class="menu-item">
        <TrendingUp :size="18" />
        Indicadores de Desempeño
      </div>

      <div class="menu-item">
        <AlertTriangle :size="18" />
        Gestion de Riesgos de Calidad
      </div>

      <div class="menu-item">
        <Lightbulb :size="18" />
        Gestion de Oportunidades
      </div>

      <div class="menu-item">
        <Rocket :size="18" />
        Proyectos de Mejora
      </div>

      <div class="menu-item">
        <ShieldCheck :size="18" />
        Auditorías
      </div>

      <div class="menu-item">
        <CalendarDays :size="18" />
        Modulo de Juntas y Sesiones
      </div>

    </div>

  </aside>
</template>


<script setup>
import { ref } from "vue";

import {
  Home,
  Search,
  Archive,
  LayoutGrid,
  Users,
  Workflow,
  FileType,
  ClipboardCheck,
  BarChart3,
  Copy,
  FolderCheck,
  RefreshCcw,
  Wrench,
  TrendingUp,
  AlertTriangle,
  Lightbulb,
  Rocket,
  ShieldCheck,
  CalendarDays
} from "lucide-vue-next";


/* =========================
   PROPS
========================= */

const props = defineProps({
  vistaActual: {
    type: String,
    default: "",
  },
});


/* =========================
   EVENTOS
========================= */

const emit = defineEmits([
  "navigate",
]);


/* =========================
   ESTADO
========================= */

const catalogos = ref(false);
const ventas = ref(false);
const reportes = ref(false);


/* =========================
   NAVEGACIÓN
========================= */

const irA = (vista) => {
  emit("navigate", vista);
};
</script>
```
