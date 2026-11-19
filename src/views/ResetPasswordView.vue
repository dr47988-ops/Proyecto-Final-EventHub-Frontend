<script setup>

import { computed, ref } from "vue";

import { useRoute } from "vue-router";



const route = useRoute();

const token = computed(() =>

  typeof route.query.token === "string" ? route.query.token : ""

);



const password = ref("");

const confirmarPassword = ref("");

const mostrarPassword = ref(false);

const error = ref("");

const exito = ref("");

const cargando = ref(false);



const cambiarPassword = async () => {

  error.value = "";

  exito.value = "";



  if (!token.value) {

    error.value = "El enlace de recuperación no es válido.";

    return;

  }

  if (password.value.length < 8 || password.value.length > 72) {

    error.value = "La contraseña debe tener entre 8 y 72 caracteres.";

    return;

  }

  if (password.value !== confirmarPassword.value) {

    error.value = "Las contraseñas no coinciden.";

    return;

  }



  cargando.value = true;

  try {

    const respuesta = await fetch("http://localhost:3000/api/auth/reset-password", {

      method: "POST",

      headers: { "Content-Type": "application/json" },

      body: JSON.stringify({ token: token.value, password: password.value })

    });

    const datos = await respuesta.json();

    if (!respuesta.ok) {

      error.value = datos.message || datos.error || "No se pudo cambiar la contraseña.";

      return;

    }

    exito.value = "Tu contraseña se cambió correctamente.";

    password.value = "";

    confirmarPassword.value = "";

  } catch (err) {

    error.value = "No se pudo conectar con el servidor.";

  } finally {

    cargando.value = false;

  }

};

</script>



<template>

  <main class="reset-page">

    <section class="welcome-panel">

      <div class="animated-background" aria-hidden="true">

        <span class="glow glow-one"></span>

        <span class="glow glow-two"></span>

        <span class="music-note note-one">♪</span>

        <span class="music-note note-two">♫</span>

        <span class="music-note note-three">♬</span>

        <span class="music-note note-four">♪</span>

        <span class="music-note note-five">♫</span>

        <span class="music-note note-six">♩</span>

        <span class="wave wave-one"></span>

        <span class="wave wave-two"></span>

        <span class="wave wave-three"></span>

      </div>



      <div class="welcome-content">

        <div class="welcome-logo">✦ EventHub</div>

        <p class="welcome-label">TU PLATAFORMA DE EVENTOS</p>

        <h1>Un nuevo comienzo <span>te está esperando.</span></h1>

        <p class="welcome-description">

          Protege tu cuenta con una nueva contraseña

          y continúa descubriendo experiencias inolvidables en EventHub.

        </p>

        <div class="welcome-features">

          <p>✦ Protege tu información personal</p>

          <p>✦ Recupera el acceso a tu cuenta</p>

          <p>✦ Sigue disfrutando de tus eventos</p>

        </div>

      </div>

    </section>



    <section class="reset-panel">

      <div class="reset-content">

        <div class="brand">

          <span class="brand-icon">✦</span>

          <h2>EventHub</h2>

        </div>

        <p class="subtitle">Tu próximo evento comienza aquí</p>

        <div class="reset-icon" aria-hidden="true">🔐</div>

        <h3>Crear nueva contraseña</h3>

        <p class="description">

          Ingresa y confirma tu nueva contraseña para recuperar el acceso a tu cuenta.

        </p>



        <p v-if="!token" class="error-message" role="alert">

          El enlace no contiene un token de recuperación válido.

        </p>



        <form v-else-if="!exito" @submit.prevent="cambiarPassword">

          <div class="field">

            <label for="password">Nueva contraseña</label>

            <input id="password" v-model="password" :type="mostrarPassword ? 'text' : 'password'"
              placeholder="Entre 8 y 72 caracteres" autocomplete="new-password" minlength="8" maxlength="72" required />

          </div>

          <div class="field">

            <label for="confirmar">Confirmar contraseña</label>

            <input id="confirmar" v-model="confirmarPassword" :type="mostrarPassword ? 'text' : 'password'"
              placeholder="Repite tu contraseña" autocomplete="new-password" minlength="8" maxlength="72" required />

          </div>

          <label class="show-password">

            <input v-model="mostrarPassword" type="checkbox" />

            Mostrar contraseñas

          </label>

          <p v-if="error" class="error-message" role="alert">{{ error }}</p>

          <button type="submit" class="reset-button" :disabled="cargando">

            {{ cargando ? "Guardando..." : "Cambiar contraseña" }}

          </button>

        </form>



        <p v-if="exito" class="success-message" role="status">{{ exito }}</p>

        <p class="back-text">

          <router-link to="/login">Volver a iniciar sesión</router-link>

        </p>

      </div>

    </section>

  </main>

</template>



<style scoped>
.reset-page {

  min-height: 100vh;

  display: grid;

  grid-template-columns: 1fr 1fr;

  background: #fff;

  font-family: Arial, sans-serif;

  box-sizing: border-box;

}

.welcome-panel {

  position: relative;

  isolation: isolate;

  overflow: hidden;

  display: flex;

  align-items: center;

  justify-content: center;

  padding: 70px;

  color: #fff;

  background: linear-gradient(145deg, #0f172a, #1e3a8a 58%, #2563eb);

}

.animated-background {

  position: absolute;

  inset: 0;

  z-index: -1;

  pointer-events: none;

  overflow: hidden;

}

.glow {

  position: absolute;

  width: 380px;

  height: 380px;

  border-radius: 50%;

  filter: blur(65px);

  background: rgba(96, 165, 250, 0.23);

  animation: glow-drift 12s ease-in-out infinite alternate;

}

.glow-one {
  top: -130px;
  right: -110px;
}

.glow-two {
  bottom: -160px;
  left: -140px;
  animation-delay: -6s;
  background: rgba(56, 189, 248, .2);
}

.music-note {

  position: absolute;

  color: rgba(219, 234, 254, .55);

  font-family: Georgia, serif;

  font-size: 38px;

  text-shadow: 0 0 22px rgba(147, 197, 253, .5);

  animation: float-note 8s ease-in-out infinite;

  user-select: none;

}

.note-one {
  left: 8%;
  top: 16%;
  animation-delay: -1s;
}

.note-two {
  right: 11%;
  top: 13%;
  font-size: 54px;
  animation-delay: -3s;
}

.note-three {
  left: 14%;
  top: 56%;
  font-size: 30px;
  animation-delay: -5s;
}

.note-four {
  right: 9%;
  top: 48%;
  font-size: 43px;
  animation-delay: -2s;
}

.note-five {
  left: 32%;
  bottom: 9%;
  font-size: 46px;
  animation-delay: -6s;
}

.note-six {
  right: 27%;
  bottom: 14%;
  font-size: 28px;
  animation-delay: -4s;
}

.wave {

  position: absolute;

  width: 150%;

  height: 310px;

  left: -25%;

  bottom: -225px;

  border: 2px solid rgba(191, 219, 254, .28);

  border-radius: 50%;

  box-shadow: 0 -9px 40px rgba(96, 165, 250, .13);

  animation: wave-motion 10s ease-in-out infinite alternate;

}

.wave-two {
  bottom: -185px;
  opacity: .65;
  animation-delay: -3s;
}

.wave-three {
  bottom: -145px;
  opacity: .38;
  animation-delay: -6s;
}

@keyframes float-note {

  0%,
  100% {
    transform: translateY(0) rotate(-9deg);
    opacity: .35;
  }

  50% {
    transform: translateY(-28px) rotate(10deg);
    opacity: .85;
  }

}

@keyframes wave-motion {

  from {
    transform: translateX(-4%) rotate(-3deg);
  }

  to {
    transform: translateX(4%) rotate(3deg);
  }

}

@keyframes glow-drift {

  from {
    transform: translate(0, 0) scale(1);
  }

  to {
    transform: translate(65px, 40px) scale(1.2);
  }

}

.welcome-content {
  position: relative;
  z-index: 1;
  width: 100%;
  max-width: 600px;
}

.welcome-logo {
  font-size: 32px;
  font-weight: 700;
  margin-bottom: 80px;
}

.welcome-label {
  color: #bfdbfe;
  font-size: 14px;
  font-weight: 700;
  letter-spacing: 3px;
  margin-bottom: 24px;
}

.welcome-content h1 {
  font-size: clamp(38px, 4vw, 64px);
  line-height: 1.15;
  margin: 0 0 28px;
}

.welcome-content h1 span {
  display: block;
  color: #93c5fd;
}

.welcome-description {
  color: #dbeafe;
  font-size: 19px;
  line-height: 1.8;
  max-width: 520px;
}

.welcome-features {
  margin-top: 45px;
}

.welcome-features p {
  color: #eff6ff;
  font-size: 17px;
  margin-bottom: 20px;
}

.reset-panel {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 60px 65px;
  box-sizing: border-box;
}

.reset-content {
  width: 100%;
  max-width: 480px;
}

.brand {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 12px;
  color: #2563eb;
}

.brand h2 {
  font-size: 36px;
  margin: 0;
}

.brand-icon {
  font-size: 34px;
}

.subtitle {
  text-align: center;
  color: #64748b;
  font-size: 16px;
  margin: 12px 0 45px;
}

.reset-icon {
  font-size: 42px;
  margin-bottom: 20px;
}

h3 {
  color: #0f172a;
  font-size: 30px;
  margin: 0 0 14px;
}

.description {
  color: #64748b;
  font-size: 17px;
  line-height: 1.7;
  margin-bottom: 30px;
}

.field {
  margin-bottom: 24px;
}

.field label {
  display: block;
  font-size: 16px;
  font-weight: 600;
  color: #334155;
  margin-bottom: 10px;
}

.field input {
  width: 100%;
  padding: 18px 20px;
  border: 1px solid #cbd5e1;
  border-radius: 12px;
  font-size: 17px;
  box-sizing: border-box;
  background: white;
  color: #0f172a;
}

.field input:focus {
  outline: none;
  border-color: #2563eb;
  box-shadow: 0 0 0 3px rgba(37, 99, 235, .12);
}

.show-password {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 26px;
  color: #334155;
  font-size: 15px;
  cursor: pointer;
}

.show-password input {
  width: 18px;
  height: 18px;
  accent-color: #2563eb;
}

.error-message {
  color: #dc2626;
  background: #fef2f2;
  padding: 14px;
  border-radius: 10px;
  font-size: 15px;
  line-height: 1.5;
  margin-bottom: 18px;
}

.success-message {
  color: #15803d;
  background: #f0fdf4;
  padding: 14px;
  border-radius: 10px;
  font-size: 15px;
  line-height: 1.5;
  margin-bottom: 18px;
}

.reset-button {
  width: 100%;
  padding: 18px;
  background: #2563eb;
  color: white;
  border: none;
  border-radius: 12px;
  font-size: 17px;
  font-weight: 700;
  cursor: pointer;
  transition: background .2s, transform .2s;
}

.reset-button:hover:not(:disabled) {
  background: #1d4ed8;
  transform: translateY(-2px);
}

.reset-button:disabled {
  background: #93c5fd;
  cursor: not-allowed;
}

.back-text {
  text-align: center;
  margin-top: 30px;
  font-size: 16px;
}

.back-text a {
  color: #2563eb;
  font-weight: 600;
  text-decoration: none;
}

.back-text a:hover {
  text-decoration: underline;
}

@media (max-width: 900px) {

  .reset-page {
    grid-template-columns: 1fr;
  }

  .welcome-panel {
    padding: 40px 28px;
  }

  .welcome-logo {
    margin-bottom: 24px;
  }

  .welcome-content h1 {
    font-size: 34px;
  }

  .welcome-description {
    font-size: 16px;
  }

  .welcome-features {
    display: none;
  }

  .reset-panel {
    padding: 50px 30px;
  }

  .reset-content {
    max-width: 600px;
  }

}

@media (max-width: 480px) {

  .welcome-panel {
    padding: 30px 22px;
  }

  .welcome-content h1 {
    font-size: 29px;
  }

  .reset-panel {
    padding: 36px 22px;
  }

  .brand h2 {
    font-size: 30px;
  }

  h3 {
    font-size: 25px;
  }

  .field input {
    font-size: 16px;
  }

  .music-note {
    font-size: 25px;
  }

}

@media (prefers-reduced-motion: reduce) {

  .music-note,
  .glow,
  .wave {
    animation: none;
  }

  .reset-button {
    transition: none;
  }

}


/* Refuerzos responsive sin cambiar la funcionalidad */
.reset-page,
.reset-page * {
  box-sizing: border-box;
}

.reset-page {
  width: 100%;
  min-width: 0;
  overflow-x: clip;
}

.welcome-panel,
.welcome-content,
.reset-panel,
.reset-content,
.reset-content form,
.field {
  min-width: 0;
  max-width: 100%;
}

.field input,
.reset-button {
  display: block;
  width: 100%;
  max-width: 100%;
}

@media (max-width: 900px) {
  .reset-page {
    display: flex;
    flex-direction: column;
    min-height: 100dvh;
  }

  .welcome-panel,
  .reset-panel {
    width: 100%;
    flex: none;
  }

  .reset-panel {
    background: #fff;
  }
}

@media (max-width: 480px) {
  .welcome-panel {
    padding: 26px 20px 28px;
  }

  .welcome-logo {
    font-size: 25px;
    margin-bottom: 14px;
  }

  .welcome-label {
    font-size: 10px;
    letter-spacing: 2px;
    margin-bottom: 12px;
  }

  .welcome-content h1 {
    font-size: clamp(24px, 7vw, 29px);
    line-height: 1.2;
    margin-bottom: 12px;
  }

  .welcome-description {
    font-size: 13px;
    line-height: 1.6;
    margin: 0;
  }

  .reset-panel {
    padding: 30px 20px 40px;
    align-items: stretch;
  }

  .reset-content {
    width: 100%;
  }

  .brand h2 {
    font-size: 28px;
  }

  .brand-icon {
    font-size: 27px;
  }

  .subtitle {
    font-size: 13px;
    margin: 9px 0 28px;
  }

  .reset-icon {
    font-size: 36px;
    margin-bottom: 12px;
  }

  h3 {
    font-size: 24px;
    margin-bottom: 10px;
  }

  .description {
    font-size: 14px;
    line-height: 1.6;
    margin-bottom: 22px;
  }

  .field {
    margin-bottom: 17px;
  }

  .field label {
    font-size: 14px;
    margin-bottom: 7px;
  }

  .field input {
    min-width: 0;
    padding: 14px;
    font-size: 16px;
  }

  .show-password {
    font-size: 14px;
  }

  .reset-button {
    padding: 15px;
    font-size: 16px;
  }

  .back-text {
    font-size: 14px;
  }

  .wave {
    height: 150px;
    bottom: -95px;
  }

  .wave-two {
    bottom: -80px;
  }

  .wave-three {
    bottom: -65px;
  }
}
</style>
