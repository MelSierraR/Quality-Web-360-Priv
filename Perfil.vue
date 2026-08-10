```vue
<template>
  <div class="ps-container">

    <!-- Mis Datos -->
    <section class="ps-card">
      <header class="ps-card__header">
        <span class="ps-card__icon" aria-hidden="true">
          👤
        </span>
        <h2>Mis Datos</h2>
      </header>

      <form class="ps-card__body" @submit.prevent="handleProfileSubmit">

        <div class="ps-avatar">
          <img
            v-if="profile.avatarUrl"
            :src="profile.avatarUrl"
            alt="Foto de perfil"
          />

          <div
            v-else
            class="ps-avatar__placeholder"
            aria-hidden="true"
          ></div>
        </div>

        <label class="ps-field">
          <span>Nombre</span>

          <input
            type="text"
            name="nombre"
            v-model="profile.nombre"
            readonly
            class="ps-input--readonly"
          />
        </label>

        <label class="ps-field">
          <span>Correo</span>

          <input
            type="email"
            name="correo"
            v-model="profile.correo"
          />
        </label>

        <label class="ps-field">
          <span>Teléfono</span>

          <input
            type="tel"
            name="telefono"
            v-model="profile.telefono"
          />
        </label>

        <label class="ps-field">
          <span>Rol</span>

          <input
            type="text"
            name="rol"
            v-model="profile.rol"
            readonly
            class="ps-input--readonly"
          />
        </label>

        <button
          type="submit"
          class="ps-btn"
        >
          Guardar Cambios
        </button>

      </form>
    </section>


    <!-- Cambiar Contraseña -->
    <section class="ps-card">

      <header class="ps-card__header">
        <span class="ps-card__icon" aria-hidden="true">
          🔑
        </span>

        <h2>Cambiar Contraseña</h2>
      </header>

      <form
        class="ps-card__body"
        @submit.prevent="handlePasswordSubmit"
      >

        <label class="ps-field">
          <span>Contraseña Actual</span>

          <input
            type="password"
            name="actual"
            v-model="passwords.actual"
            required
          />
        </label>

        <label class="ps-field">
          <span>Nueva Contraseña</span>

          <input
            type="password"
            name="nueva"
            v-model="passwords.nueva"
            required
          />
        </label>

        <label class="ps-field">
          <span>Confirmar Contraseña</span>

          <input
            type="password"
            name="confirmar"
            v-model="passwords.confirmar"
            required
          />
        </label>

        <button
          type="submit"
          class="ps-btn"
        >
          Guardar Cambios
        </button>

      </form>
    </section>

  </div>
</template>


<script setup>
import { ref } from "vue";
import "./Perfil.css";


/*
 * Perfil
 * Página "Mis Datos" + "Cambiar Contraseña"
 * en la paleta institucional verde/gris/blanco
 * (WEB QUALITY 360).
 *
 * Props opcionales:
 *
 * user: {
 *   nombre,
 *   correo,
 *   telefono,
 *   rol,
 *   avatarUrl
 * }
 */

const props = defineProps({
  user: {
    type: Object,
    default: () => ({
      nombre: "",
      correo: "",
      telefono: "",
      rol: "",
      avatarUrl: "",
    }),
  },
});


const emit = defineEmits([
  "saveProfile",
  "savePassword",
]);


/* =========================
   DATOS DEL PERFIL
========================= */

const profile = ref({
  nombre: props.user.nombre || "",
  correo: props.user.correo || "",
  telefono: props.user.telefono || "",
  rol: props.user.rol || "",
  avatarUrl: props.user.avatarUrl || "",
});


/* =========================
   CONTRASEÑAS
========================= */

const passwords = ref({
  actual: "",
  nueva: "",
  confirmar: "",
});


/* =========================
   GUARDAR PERFIL
========================= */

const handleProfileSubmit = () => {
  emit("saveProfile", {
    ...profile.value,
  });
};


/* =========================
   GUARDAR CONTRASEÑA
========================= */

const handlePasswordSubmit = () => {

  if (
    passwords.value.nueva !==
    passwords.value.confirmar
  ) {
    alert(
      "La nueva contraseña y su confirmación no coinciden."
    );

    return;
  }

  emit("savePassword", {
    ...passwords.value,
  });

  passwords.value = {
    actual: "",
    nueva: "",
    confirmar: "",
  };
};
</script>
```
