```vue
<template>
  <header class="navbar">

    <div class="logo">
      WEB QUALITY 360
    </div>

    <div class="navbar-right">

      <div class="dash__header-actions">

        <button
          class="dash__icon-btn"
          title="Avisos"
        >
          📢
        </button>

        <button
          class="dash__icon-btn"
          title="Notificaciones"
        >
          🔔
        </button>

        <div class="dash__avatar"></div>

      </div>

      <button
        class="dash__icon-btn"
        title="Perfil"
        @click="showProfile = true"
      >
        👤
      </button>

    </div>


    <!-- Modal de Perfil -->
    <div
      v-if="showProfile"
      class="ps-overlay"
      @click="showProfile = false"
    >

      <div
        class="ps-overlay__content"
        @click.stop
      >

        <button
          class="ps-overlay__close"
          @click="showProfile = false"
        >
          ✕
        </button>

        <Perfil
          @saveProfile="guardarPerfil"
          @savePassword="guardarPassword"
        />

      </div>

    </div>

  </header>
</template>


<script setup>
import { ref } from "vue";
import Perfil from "./Perfil.vue";


/* =========================
   ESTADO
========================= */

const showProfile = ref(false);


/* =========================
   GUARDAR PERFIL
========================= */

const guardarPerfil = (data) => {
  console.log(data);

  showProfile.value = false;
};


/* =========================
   GUARDAR CONTRASEÑA
========================= */

const guardarPassword = (data) => {
  console.log(data);
};
</script>
```
