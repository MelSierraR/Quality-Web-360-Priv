```vue
<template>
  <div class="lu-container">
    <div class="lu-card">
      <header class="lu-card__header">
        <h2>Lista de Usuarios</h2>
      </header>

      <div class="lu-card__body">

        <!-- Fila 1: Nuevo Usuario -->
        <div class="lu-toolbar-nuevo">
          <button
            class="lu-btn lu-btn--nuevo lu-btn--sm"
            @click="modalUsuario = 'nuevo'"
          >
            <span aria-hidden="true">➕</span> Nuevo Usuario
          </button>
        </div>

        <!-- Fila 2: Buscar + Exportar -->
        <div class="lu-toolbar">
          <label class="lu-search">
            Buscar:
            <input
              type="text"
              v-model="busqueda"
              @input="pagina = 1"
            />
          </label>

          <button
            class="lu-btn lu-btn--outline"
            @click="exportarCSV(usuariosFiltrados)"
          >
            Exportar Excel
          </button>
        </div>

        <!-- Fila 3: Mostrar filas -->
        <div class="lu-toolbar-mostrar">
          <label class="lu-select-inline">
            Mostrar
            <select v-model.number="filasPorPagina" @change="pagina = 1">
              <option v-for="n in [5, 10, 25, 50]" :key="n" :value="n">
                {{ n }}
              </option>
            </select>
            filas
          </label>
        </div>

        <!-- Tabla -->
        <div class="lu-table-wrap">
          <table class="lu-table">
            <thead>
              <tr>
                <th>Foto</th>
                <th>Nombre</th>
                <th>Correo</th>
                <th>Teléfono</th>
                <th>Puesto</th>
                <th>Estado</th>
                <th>Acciones</th>
              </tr>
            </thead>

            <tbody>
              <tr v-for="u in usuariosPagina" :key="u.id">
                <td>
                  <div class="lu-avatar">
                    <img
                      v-if="u.fotoUrl"
                      :src="u.fotoUrl"
                      :alt="u.nombre"
                    />
                  </div>
                </td>

                <td>{{ u.nombre }}</td>
                <td class="lu-cell--link">{{ u.correo }}</td>
                <td>{{ u.telefono }}</td>
                <td>{{ u.puesto }}</td>

                <td>
                  <span
                    :class="[
                      'lu-badge',
                      u.estado === 'Activo'
                        ? 'lu-badge--activo'
                        : 'lu-badge--inactivo',
                    ]"
                  >
                    {{ u.estado }}
                  </span>
                </td>

                <td>
                  <div class="lu-actions">
                    <button
                      class="lu-icon-btn lu-icon-btn--edit"
                      title="Editar"
                      @click="modalUsuario = u"
                    >
                      ✏️
                    </button>

                    <button
                      class="lu-icon-btn lu-icon-btn--delete"
                      title="Eliminar"
                      @click="usuarioABorrar = u"
                    >
                      🗑️
                    </button>
                  </div>
                </td>
              </tr>

              <tr v-if="usuariosPagina.length === 0">
                <td colspan="7" class="lu-empty">
                  No se encontraron usuarios.
                </td>
              </tr>
            </tbody>
          </table>
        </div>

        <!-- Paginación -->
        <div class="lu-pagination">
          <span class="lu-pagination__info">
            Página {{ pagina }} de {{ totalPaginas }}
          </span>

          <div class="lu-pagination__btns">
            <button
              class="lu-btn lu-btn--outline"
              :disabled="pagina <= 1"
              @click="pagina = Math.max(1, pagina - 1)"
            >
              Anterior
            </button>

            <button
              class="lu-btn lu-btn--outline"
              :disabled="pagina >= totalPaginas"
              @click="pagina = Math.min(totalPaginas, pagina + 1)"
            >
              Siguiente
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- ========================= -->
    <!-- MODAL USUARIO -->
    <!-- ========================= -->

    <div
      v-if="modalUsuario"
      class="lu-overlay"
      @click="cerrarModalUsuario"
    >
      <div class="lu-modal" @click.stop>

        <header class="lu-modal__header">
          <h2>
            {{ esNuevo ? "Nuevo Usuario" : "Editar Usuario" }}
          </h2>
        </header>

        <form
          class="lu-modal__body"
          @submit.prevent="handleSubmit"
          novalidate
        >

          <div
            v-if="Object.keys(errores).length > 0"
            class="lu-form-error-banner"
          >
            Revisa los campos marcados: hay datos incorrectos o incompletos.
          </div>

          <!-- Fotografía -->
          <div class="lu-foto-field">
            <span class="lu-foto-field__label">
              Fotografía de Perfil
            </span>

            <div class="lu-foto-opciones">
              <button
                v-for="foto in FOTOS_PERFIL"
                :key="foto.id"
                type="button"
                :class="[
                  'lu-foto-opcion',
                  form.fotoUrl === foto.url
                    ? 'lu-foto-opcion--seleccionada'
                    : '',
                ]"
                @click="form.fotoUrl = foto.url"
                :title="foto.etiqueta"
              >
                <div class="lu-avatar lu-avatar--lg">
                  <img
                    v-if="foto.url"
                    :src="foto.url"
                    :alt="foto.etiqueta"
                  />
                </div>
              </button>
            </div>

            <small
              v-if="errores.fotoUrl"
              class="lu-error-text"
            >
              {{ errores.fotoUrl }}
            </small>
          </div>

          <!-- Nombre -->
          <label class="lu-field">
            <span>Nombre</span>

            <input
              type="text"
              v-model="form.nombre"
              :class="errores.nombre ? 'lu-input--error' : ''"
            />

            <small
              v-if="errores.nombre"
              class="lu-error-text"
            >
              {{ errores.nombre }}
            </small>
          </label>

          <!-- Correo -->
          <label class="lu-field">
            <span>Correo Electrónico</span>

            <input
              type="email"
              v-model="form.correo"
              :class="errores.correo ? 'lu-input--error' : ''"
            />

            <small
              v-if="errores.correo"
              class="lu-error-text"
            >
              {{ errores.correo }}
            </small>
          </label>

          <!-- Teléfono -->
          <label class="lu-field">
            <span>Teléfono</span>

            <input
              type="tel"
              v-model="form.telefono"
            />
          </label>

          <!-- Puesto -->
          <label class="lu-field">
            <span>Puesto /Permisos</span>

            <select
              v-model="form.puesto"
              :class="errores.puesto ? 'lu-input--error' : ''"
            >
              <option
                v-for="p in PUESTO"
                :key="p"
                :value="p"
              >
                {{ p }}
              </option>
            </select>

            <small
              v-if="errores.puesto"
              class="lu-error-text"
            >
              {{ errores.puesto }}
            </small>
          </label>

          <!-- Contraseña -->
          <label class="lu-field">
            <span>
              {{
                esNuevo
                  ? "Contraseña Asignada"
                  : "Nueva Contraseña (opcional)"
              }}
            </span>

            <input
              type="password"
              v-model="form.contrasena"
              :class="errores.contrasena ? 'lu-input--error' : ''"
              :placeholder="
                esNuevo
                  ? ''
                  : 'Dejar en blanco para no cambiarla'
              "
            />

            <small
              v-if="errores.contrasena"
              class="lu-error-text"
            >
              {{ errores.contrasena }}
            </small>
          </label>

          <!-- Estado -->
          <label class="lu-field">
            <span>Estado</span>

            <span
              v-if="esNuevo"
              class="lu-badge lu-badge--activo lu-badge--static"
            >
              Activo
            </span>

            <select
              v-else
              v-model="form.estado"
            >
              <option
                v-for="e in ESTADOS"
                :key="e"
                :value="e"
              >
                {{ e }}
              </option>
            </select>
          </label>

          <!-- Acciones -->
          <div class="lu-modal__actions">
            <button
              type="button"
              class="lu-btn lu-btn--ghost"
              @click="cerrarModalUsuario"
            >
              Cancelar
            </button>

            <button
              type="submit"
              class="lu-btn"
            >
              Guardar
            </button>
          </div>

        </form>
      </div>
    </div>

    <!-- ========================= -->
    <!-- MODAL ELIMINAR -->
    <!-- ========================= -->

    <div
      v-if="usuarioABorrar"
      class="lu-overlay"
      @click="cancelarBorrado"
    >
      <div
        class="lu-modal lu-modal--sm"
        @click.stop
      >
        <header class="lu-modal__header">
          <h2>Eliminar Usuario</h2>
        </header>

        <div class="lu-modal__body">
          <p>
            ¿Seguro que quieres eliminar a
            <strong>{{ usuarioABorrar.nombre }}</strong>?
            Esta acción no se puede deshacer.
          </p>

          <div class="lu-modal__actions">
            <button
              class="lu-btn lu-btn--ghost"
              @click="cancelarBorrado"
            >
              Cancelar
            </button>

            <button
              class="lu-btn lu-btn--danger"
              @click="handleConfirmarBorrado"
            >
              Eliminar
            </button>
          </div>
        </div>
      </div>
    </div>

  </div>
</template>


<script setup>
import { ref, computed, watch } from "vue";
import "./ListaUsuarios.css";


/* =========================
   DATOS
========================= */

const USUARIOS_MOCK = [];

const usuarios = ref([...USUARIOS_MOCK]);
const busqueda = ref("");
const filasPorPagina = ref(10);
const pagina = ref(1);

const modalUsuario = ref(null);
const usuarioABorrar = ref(null);


/* =========================
   OPCIONES DEL FORMULARIO
========================= */

const PUESTO = [
  "Administrador",
  "Supervisor",
  "Usuario",
];

const ESTADOS = [
  "Activo",
  "Inactivo",
];

const FOTOS_PERFIL = [
  {
    id: 1,
    url: "",
    etiqueta: "Sin fotografía",
  },
  {
    id: 2,
    url: "https://i.pravatar.cc/150?img=1",
    etiqueta: "Fotografía 1",
  },
  {
    id: 3,
    url: "https://i.pravatar.cc/150?img=2",
    etiqueta: "Fotografía 2",
  },
  {
    id: 4,
    url: "https://i.pravatar.cc/150?img=3",
    etiqueta: "Fotografía 3",
  },
];


/* =========================
   FORMULARIO
========================= */

const form = ref({
  id: null,
  fotoUrl: "",
  nombre: "",
  correo: "",
  telefono: "",
  puesto: PUESTO[0],
  contrasena: "",
  estado: "Activo",
});

const errores = ref({});


const esNuevo = computed(() => {
  return modalUsuario.value === "nuevo";
});


/* =========================
   USUARIOS FILTRADOS
========================= */

const usuariosFiltrados = computed(() => {
  const termino = busqueda.value.trim().toLowerCase();

  if (!termino) {
    return usuarios.value;
  }

  return usuarios.value.filter((u) =>
    [
      u.nombre,
      u.correo,
      u.telefono,
      u.puesto,
      u.estado,
    ]
      .join(" ")
      .toLowerCase()
      .includes(termino)
  );
});


/* =========================
   PAGINACIÓN
========================= */

const totalPaginas = computed(() =>
  Math.max(
    1,
    Math.ceil(
      usuariosFiltrados.value.length /
        filasPorPagina.value
    )
  )
);

const usuariosPagina = computed(() =>
  usuariosFiltrados.value.slice(
    (pagina.value - 1) *
      filasPorPagina.value,
    pagina.value *
      filasPorPagina.value
  )
);


/* =========================
   ABRIR / CERRAR MODAL
========================= */

watch(modalUsuario, (valor) => {
  errores.value = {};

  if (valor === "nuevo") {
    form.value = {
      id: null,
      fotoUrl: "",
      nombre: "",
      correo: "",
      telefono: "",
      puesto: PUESTO[0],
      contrasena: "",
      estado: "Activo",
    };

    return;
  }

  if (valor && typeof valor === "object") {
    form.value = {
      id: valor.id,
      fotoUrl: valor.fotoUrl || "",
      nombre: valor.nombre || "",
      correo: valor.correo || "",
      telefono: valor.telefono || "",
      puesto: valor.puesto || PUESTO[0],
      contrasena: "",
      estado: valor.estado || "Activo",
    };
  }
});


function cerrarModalUsuario() {
  modalUsuario.value = null;
  errores.value = {};
}


/* =========================
   VALIDACIÓN
========================= */

function validarFormulario() {
  const nuevosErrores = {};

  if (!form.value.fotoUrl) {
    nuevosErrores.fotoUrl =
      "Selecciona una fotografía de perfil.";
  }

  if (!form.value.nombre.trim()) {
    nuevosErrores.nombre =
      "El nombre es obligatorio.";
  }

  if (!form.value.correo.trim()) {
    nuevosErrores.correo =
      "El correo electrónico es obligatorio.";
  } else {
    const correoValido =
      /^[^\s@]+@[^\s@]+\.[^\s@]+$/;

    if (!correoValido.test(form.value.correo)) {
      nuevosErrores.correo =
        "Ingresa un correo electrónico válido.";
    }
  }

  if (!form.value.puesto) {
    nuevosErrores.puesto =
      "Selecciona un puesto.";
  }

  if (
    esNuevo.value &&
    !form.value.contrasena.trim()
  ) {
    nuevosErrores.contrasena =
      "La contraseña es obligatoria.";
  }

  errores.value = nuevosErrores;

  return Object.keys(nuevosErrores).length === 0;
}


/* =========================
   GUARDAR USUARIO
========================= */

function handleSubmit() {
  if (!validarFormulario()) {
    return;
  }

  handleGuardarUsuario(form.value);
}


function handleGuardarUsuario(formulario) {
  const { contrasena, ...datosUsuario } =
    formulario;

  if (modalUsuario.value === "nuevo") {
    usuarios.value.push({
      ...datosUsuario,
      id: Date.now(),
    });
  } else {
    usuarios.value = usuarios.value.map((u) =>
      u.id === formulario.id
        ? {
            ...datosUsuario,
            id: formulario.id,
          }
        : u
    );
  }

  modalUsuario.value = null;
  errores.value = {};
}


/* =========================
   ELIMINAR USUARIO
========================= */

function cancelarBorrado() {
  usuarioABorrar.value = null;
}


function handleConfirmarBorrado() {
  if (!usuarioABorrar.value) {
    return;
  }

  usuarios.value = usuarios.value.filter(
    (u) =>
      u.id !== usuarioABorrar.value.id
  );

  usuarioABorrar.value = null;
}


/* =========================
   EXPORTAR CSV
========================= */

function exportarCSV(lista) {
  const encabezados = [
    "Nombre",
    "Correo",
    "Telefono",
    "Puesto",
    "Estado",
  ];

  const filas = lista.map((u) => [
    u.nombre,
    u.correo,
    u.telefono,
    u.puesto,
    u.estado,
  ]);

  const contenido = [
    encabezados,
    ...filas,
  ]
    .map((fila) =>
      fila
        .map(
          (campo) =>
            `"${String(campo).replace(
              /"/g,
              '""'
            )}"`
        )
        .join(",")
    )
    .join("\n");

  const blob = new Blob(
    ["\uFEFF" + contenido],
    {
      type: "text/csv;charset=utf-8;",
    }
  );

  const url =
    URL.createObjectURL(blob);

  const enlace =
    document.createElement("a");

  enlace.href = url;
  enlace.download = "usuarios.csv";
  enlace.click();

  URL.revokeObjectURL(url);
}
</script>
```
