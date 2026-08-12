<template>
  <div class="auditores-card">
    <div class="auditores-header">
      <h2>Catálogo de Auditores</h2>
    </div>

    <div class="auditores-body">
      <div class="auditores-toolbar">
        <button class="auditores-btn auditores-btn--nuevo" @click="abrirNuevoAuditor">
        <span aria-hidden="true">➕</span> Nuevo Indicador
        </button>
      </div>

      <div class="auditores-filtros">
        <label class="auditores-buscar">
          Buscar:
          <input type="text" v-model="busqueda" placeholder="Buscar auditor..." />
        </label>
      </div>

      <div class="auditores-tabla-wrapper">
        <table class="auditores-tabla">
          <thead>
            <tr>
              <th>Código</th>
              <th>Nombre</th>
              <th>Departamento</th>
              <th>Correo</th>
              <th>Situación</th>
              <th>Acciones</th>
            </tr>
          </thead>

          <tbody>
            <tr v-for="auditor in auditoresFiltrados" :key="auditor.id">
              <td>{{ auditor.codigo }}</td>
              <td>{{ auditor.nombre }}</td>
              <td>{{ auditor.departamento }}</td>
              <td class="auditores-correo">{{ auditor.correo }}</td>

              <td>
                <span
                  :class="['auditores-estado', auditor.situacion === 'Libre' ? 'auditores-estado--libre' : 'auditores-estado--labores']"
                >
                  {{ auditor.situacion }}
                </span>
              </td>

              <td>
                <div class="auditores-acciones">
                  <button
                    class="auditores-icono auditores-icono--editar"
                    title="Editar"
                    @click="editarAuditor(auditor)"
                  >
                    ✏️
                  </button>

                  <button
                    class="auditores-icono auditores-icono--eliminar"
                    title="Eliminar"
                    @click="eliminarAuditor(auditor)"
                  >
                    🗑️
                  </button>
                </div>
              </td>
            </tr>

            <tr v-if="auditoresFiltrados.length === 0">
              <td colspan="6" class="auditores-vacio">
                No hay auditores registrados.
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <div class="auditores-paginacion">
        <span>Página 1 de 1</span>

        <div>
          <button class="auditores-btn auditores-btn--outline" disabled>Anterior</button>
          <button class="auditores-btn auditores-btn--outline" disabled>Siguiente</button>
        </div>
      </div>
    </div>
  </div>

  <div v-if="modalAuditor" class="auditores-overlay" @click="cerrarModal">
    <div class="auditores-modal" @click.stop>
      <div class="auditores-modal-header">
        <h2>{{ esNuevo ? "Nuevo Auditor" : "Editar Auditor" }}</h2>
      </div>

      <form class="auditores-modal-body" @submit.prevent="guardarAuditor">
        <label class="auditores-field">
          <span>Código del Auditor</span>
          <input
            type="text"
            v-model="form.codigo"
            placeholder="Ej. AUD-001"
            required
          />
        </label>

        <label class="auditores-field">
          <span>Usuario</span>
          <select v-model="form.usuarioId" @change="seleccionarUsuario" required>
            <option value="" disabled>Selecciona un usuario</option>
            <option v-for="usuario in usuarios" :key="usuario.id" :value="usuario.id">
              {{ usuario.nombre }}
            </option>
          </select>
        </label>

        <label class="auditores-field">
          <span>Nombre</span>
          <input type="text" v-model="form.nombre" readonly />
        </label>

        <label class="auditores-field">
          <span>Departamento</span>
          <input type="text" v-model="form.departamento" readonly />
        </label>

        <label class="auditores-field">
          <span>Correo electrónico</span>
          <input type="email" v-model="form.correo" readonly />
        </label>

        <label class="auditores-field">
          <span>Situación</span>
          <select v-model="form.situacion">
            <option value="Libre">Libre</option>
            <option value="En labores">En labores</option>
          </select>
        </label>

        <div class="auditores-modal-acciones">
          <button type="button" class="auditores-btn auditores-btn--cancelar" @click="cerrarModal">
            Cancelar
          </button>
          <button type="submit" class="auditores-btn auditores-btn--guardar">
            Guardar
          </button>
        </div>
      </form>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from "vue";
import "./Auditores.css";

// Usuarios de prueba: temporales, se sustituirán por los usuarios reales
// cuando se conecte la API/base de datos.
const usuarios = ref([
  { id: 1, nombre: "María López", departamento: "Calidad", correo: "maria.lopez@empresa.com", estado: "Activo" },
  { id: 2, nombre: "Carlos Hernández", departamento: "Producción", correo: "carlos.hernandez@empresa.com", estado: "Activo" },
  { id: 3, nombre: "Ana Martínez", departamento: "Recursos Humanos", correo: "ana.martinez@empresa.com", estado: "Activo" },
  { id: 4, nombre: "Luis García", departamento: "Administración", correo: "luis.garcia@empresa.com", estado: "Activo" },
]);

const auditores = ref([]);
const busqueda = ref("");

const auditoresFiltrados = computed(() => {
  const texto = busqueda.value.trim().toLowerCase();

  if (!texto) {
    return auditores.value;
  }

  return auditores.value.filter((auditor) =>
    [auditor.codigo, auditor.nombre, auditor.departamento, auditor.correo, auditor.situacion]
      .join(" ")
      .toLowerCase()
      .includes(texto)
  );
});

const modalAuditor = ref(false);
const esNuevo = ref(true);

const form = ref({
  id: null,
  codigo: "",
  usuarioId: "",
  nombre: "",
  departamento: "",
  correo: "",
  situacion: "Libre",
});

function abrirNuevoAuditor() {
  esNuevo.value = true;

  form.value = {
    id: null,
    codigo: "",
    usuarioId: "",
    nombre: "",
    departamento: "",
    correo: "",
    situacion: "Libre",
  };

  modalAuditor.value = true;
}

function seleccionarUsuario() {
  const usuario = usuarios.value.find((u) => u.id === Number(form.value.usuarioId));

  if (!usuario) {
    return;
  }

  form.value.nombre = usuario.nombre;
  form.value.departamento = usuario.departamento;
  form.value.correo = usuario.correo;
}

function editarAuditor(auditor) {
  esNuevo.value = false;

  form.value = {
    id: auditor.id,
    codigo: auditor.codigo,
    usuarioId: auditor.usuarioId,
    nombre: auditor.nombre,
    departamento: auditor.departamento,
    correo: auditor.correo,
    situacion: auditor.situacion,
  };

  modalAuditor.value = true;
}

function guardarAuditor() {
  if (!form.value.usuarioId) {
    alert("Selecciona un usuario.");
    return;
  }

  if (!form.value.codigo.trim()) {
    alert("Ingresa el código del auditor.");
    return;
  }

  if (esNuevo.value) {
    auditores.value.push({
      id: Date.now(),
      codigo: form.value.codigo,
      usuarioId: Number(form.value.usuarioId),
      nombre: form.value.nombre,
      departamento: form.value.departamento,
      correo: form.value.correo,
      situacion: form.value.situacion,
    });
  } else {
    auditores.value = auditores.value.map((auditor) =>
      auditor.id === form.value.id
        ? {
            ...auditor,
            codigo: form.value.codigo,
            usuarioId: Number(form.value.usuarioId),
            nombre: form.value.nombre,
            departamento: form.value.departamento,
            correo: form.value.correo,
            situacion: form.value.situacion,
          }
        : auditor
    );
  }

  cerrarModal();
}

function eliminarAuditor(auditor) {
  const confirmar = confirm(
    `¿Seguro que deseas eliminar a ${auditor.nombre} del catálogo de auditores?`
  );

  if (!confirmar) {
    return;
  }

  auditores.value = auditores.value.filter((a) => a.id !== auditor.id);
}

function cerrarModal() {
  modalAuditor.value = false;
}
</script>