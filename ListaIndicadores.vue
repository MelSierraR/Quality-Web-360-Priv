<template>
  <div class="indicadores-container">
    <div class="indicadores-card">
      <div class="indicadores-header">
        <h2>Catálogo de Indicadores</h2>
      </div>

      <div class="indicadores-body">
        <div class="indicadores-toolbar">
          <button class="btn-nuevo" type="button" @click="abrirModal">
            <span aria-hidden="true">➕</span> Nuevo Indicador
          </button>
        </div>

        <div class="lu-toolbar">
          <label class="lu-search">
            Buscar:
            <input type="text" v-model="busqueda" @input="pagina = 1" />
          </label>

          <button
            class="lu-btn lu-btn--outline"
            @click="exportarCSV(indicadoresFiltrados)"
          >
            Exportar Excel
          </button>
        </div>

        <div class="indicadores-filtros">
          <select v-model="filasPorPagina" class="select-filas">
            <option value="10">Mostrar 10 filas</option>
            <option value="25">Mostrar 25 filas</option>
            <option value="50">Mostrar 50 filas</option>
          </select>
        </div>

        <div class="tabla-wrapper">
          <table class="tabla-indicadores">
            <thead>
              <tr>
                <th>Código</th>
                <th>Nombre del Indicador</th>
                <th>Proceso</th>
                <th>Responsable</th>
                <th>Frecuencia</th>
                <th>Meta</th>
                <th>Estado</th>
                <th>Acciones</th>
              </tr>
            </thead>

            <tbody>
              <tr v-if="indicadoresFiltrados.length === 0">
                <td colspan="8" class="sin-indicadores">
                  No se encontraron indicadores.
                </td>
              </tr>

              <tr v-for="indicador in indicadoresFiltrados" :key="indicador.codigo">
                <td>{{ indicador.codigo }}</td>
                <td>{{ indicador.nombre }}</td>
                <td>{{ indicador.proceso }}</td>
                <td>{{ indicador.responsable }}</td>
                <td>{{ indicador.frecuencia }}</td>
                <td>{{ indicador.meta }}</td>

                <td>
                  <span :class="['estado', indicador.activo ? 'activo' : 'inactivo']">
                    {{ indicador.activo ? 'Activo' : 'No Activo' }}
                  </span>
                </td>

                <td>
                  <div class="acciones">
                    <button class="btn-editar" type="button" title="Editar">
                      <Pencil :size="15" />
                    </button>

                    <button class="btn-eliminar" type="button" title="Eliminar">
                      <Trash2 :size="15" />
                    </button>
                  </div>
                </td>
              </tr>
            </tbody>
          </table>
        </div>

        <div class="paginacion">
          <span>Página 1 de 1</span>

          <div>
            <button type="button" disabled>Anterior</button>
            <button type="button" disabled>Siguiente</button>
          </div>
        </div>
      </div>
    </div>
  </div>

  <div v-if="mostrarModal" class="modal-overlay" @click.self="cerrarModal">
    <div class="modal-indicador">
      <div class="modal-header">
        <h3>Nuevo Indicador</h3>
        <button class="modal-cerrar" type="button" @click="cerrarModal">×</button>
      </div>

      <form class="form-indicador" @submit.prevent="guardarIndicador">
        <div class="campo">
          <label for="codigo">Código</label>
          <input
            id="codigo"
            v-model="nuevoIndicador.codigo"
            type="text"
            placeholder="Ej. IND-001"
            required
          />
        </div>

        <div class="campo">
          <label for="nombre">Nombre del Indicador</label>
          <input
            id="nombre"
            v-model="nuevoIndicador.nombre"
            type="text"
            placeholder="Ej. Cumplimiento de objetivos"
            required
          />
        </div>

        <div class="campo">
          <label for="proceso">Proceso</label>
          <select id="proceso" v-model="nuevoIndicador.proceso" required>
            <option value="" disabled>Selecciona un proceso</option>
            <option>Planeación Estratégica</option>
            <option>Servicio al Cliente</option>
            <option>Logística</option>
            <option>Auditoría</option>
            <option>Recursos Humanos</option>
            <option>Gestión de Calidad</option>
          </select>
        </div>

        <div class="campo">
          <label for="responsable">Responsable</label>
          <input
            id="responsable"
            v-model="nuevoIndicador.responsable"
            type="text"
            placeholder="Ej. Gerencia"
            required
          />
        </div>

        <div class="campo">
          <label for="frecuencia">Frecuencia</label>
          <select id="frecuencia" v-model="nuevoIndicador.frecuencia" required>
            <option value="" disabled>Selecciona una frecuencia</option>
            <option>Mensual</option>
            <option>Trimestral</option>
            <option>Semestral</option>
            <option>Anual</option>
          </select>
        </div>

        <div class="campo">
          <label for="meta">Meta</label>
          <input
            id="meta"
            v-model="nuevoIndicador.meta"
            type="text"
            placeholder="Ej. 90%"
            required
          />
        </div>

        <div class="campo">
          <label for="estado">Estado</label>
          <select id="estado" v-model="nuevoIndicador.activo">
            <option :value="true">Activo</option>
            <option :value="false">No Activo</option>
          </select>
        </div>

        <div class="modal-botones">
          <button type="button" class="btn-cancelar" @click="cerrarModal">
            Cancelar
          </button>
          <button type="submit" class="btn-guardar">Guardar</button>
        </div>
      </form>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from "vue";
import { Pencil, Trash2 } from "lucide-vue-next";

const busqueda = ref("");
const filasPorPagina = ref("10");
const mostrarModal = ref(false);
const indicadores = ref([]);

const nuevoIndicador = ref({
  codigo: "",
  nombre: "",
  proceso: "",
  responsable: "",
  frecuencia: "",
  meta: "",
  activo: true,
});

const abrirModal = () => {
  nuevoIndicador.value = {
    codigo: "",
    nombre: "",
    proceso: "",
    responsable: "",
    frecuencia: "",
    meta: "",
    activo: true,
  };

  mostrarModal.value = true;
};

const cerrarModal = () => {
  mostrarModal.value = false;
};

const guardarIndicador = () => {
  const indicador = { ...nuevoIndicador.value };
  indicadores.value.push(indicador);
  cerrarModal();
};

const indicadoresFiltrados = computed(() => {
  const texto = busqueda.value.toLowerCase().trim();

  if (indicadores.value.length === 0) {
    return [];
  }

  if (!texto) {
    return indicadores.value;
  }

  return indicadores.value.filter((indicador) => {
    return (
      indicador.codigo.toLowerCase().includes(texto) ||
      indicador.nombre.toLowerCase().includes(texto) ||
      indicador.proceso.toLowerCase().includes(texto) ||
      indicador.responsable.toLowerCase().includes(texto)
    );
  });
});
</script>

<style src="./ListaIndicadores.css"></style>