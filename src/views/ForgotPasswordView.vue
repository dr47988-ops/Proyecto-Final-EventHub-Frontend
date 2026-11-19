<script setup>
import { ref } from "vue";

const email = ref("");
const error = ref("");
const exito = ref("");
const cargando = ref(false);

const recuperarPassword = async () => {
  error.value = "";
  exito.value = "";

  if (!email.value.trim()) {
    error.value = "Ingresa tu correo electrónico.";
    return;
  }

  cargando.value = true;

  try {
    const respuesta = await fetch(
      "http://localhost:3000/api/auth/forgot-password",
      {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ email: email.value.trim() })
      }
    );

    const datos = await respuesta.json();

    if (!respuesta.ok) {
      error.value = datos.message || "No se pudo procesar la solicitud.";
      return;
    }

    exito.value = datos.message ||
      "Si el correo está registrado, recibirás un enlace para recuperar tu contraseña.";
    email.value = "";
  } catch (err) {
    error.value = "No se pudo conectar con el servidor. Inténtalo más tarde.";
  } finally {
    cargando.value = false;
  }
};
</script>

<template>
  <main class="recovery-page">
    <section class="welcome-panel">
      <div class="ambient-glow ambient-glow-one" aria-hidden="true"></div>
      <div class="ambient-glow ambient-glow-two" aria-hidden="true"></div>
      <div class="music-notes" aria-hidden="true">
        <span class="music-note note-1">♪</span>
        <span class="music-note note-2">♫</span>
        <span class="music-note note-3">♬</span>
        <span class="music-note note-4">♪</span>
        <span class="music-note note-5">♫</span>
        <span class="music-note note-6">♩</span>
        <span class="music-note note-7">♪</span>
      </div>
      <div class="light-waves" aria-hidden="true">
        <span class="light-wave wave-1"></span>
        <span class="light-wave wave-2"></span>
        <span class="light-wave wave-3"></span>
      </div>

      <div class="welcome-content">
        <div class="welcome-logo">✦ EventHub</div>
        <p class="welcome-label">TU PLATAFORMA DE EVENTOS</p>
        <h1>
          Siempre hay una forma
          <span>de volver a conectar.</span>
        </h1>
        <p class="welcome-description">
          Recupera el acceso a tu cuenta y continúa
          disfrutando de todas las experiencias
          que EventHub tiene para ti.
        </p>
        <div class="welcome-features">
          <p>✦ Recupera tu acceso fácilmente</p>
          <p>✦ Mantén tu cuenta protegida</p>
          <p>✦ Continúa viviendo nuevas experiencias</p>
        </div>
      </div>
    </section>

    <section class="recovery-panel">
      <div class="recovery-content">
        <div class="brand">
          <span class="brand-icon">✦</span>
          <h2>EventHub</h2>
        </div>
        <p class="subtitle">Tu próximo evento comienza aquí</p>
        <div class="recovery-icon" aria-hidden="true">🔐</div>
        <h3>Recuperar contraseña</h3>
        <p class="description">
          Ingresa tu correo electrónico y te enviaremos
          un enlace para crear una nueva contraseña.
        </p>

        <form @submit.prevent="recuperarPassword">
          <div class="field">
            <label for="email">Correo electrónico</label>
            <input id="email" v-model="email" type="email" placeholder="correo@ejemplo.com" autocomplete="email"
              required />
          </div>
          <p v-if="error" class="error-message" role="alert">{{ error }}</p>
          <p v-if="exito" class="success-message" role="status">{{ exito }}</p>
          <button type="submit" class="recovery-button" :disabled="cargando">
            {{ cargando ? "Enviando solicitud..." : "Enviar enlace de recuperación" }}
          </button>
        </form>

        <p class="back-text">
          ¿Recordaste tu contraseña?
          <router-link to="/login">Volver a iniciar sesión</router-link>
        </p>
      </div>
    </section>
  </main>
</template>

<style scoped>
.recovery-page {
  min-height: 100vh;
  display: grid;
  grid-template-columns: 1fr 1fr;
  background: #fff;
  font-family: Arial, sans-serif;
  box-sizing: border-box;
}

/* Panel musical animado */
.welcome-panel {
  position: relative;
  isolation: isolate;
  overflow: hidden;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 70px;
  color: white;
  background: linear-gradient(145deg, #0f172a 0%, #1e3a8a 53%, #2563eb 100%);
}

.welcome-panel::before {
  content: "";
  position: absolute;
  inset: 0;
  z-index: -1;
  background: radial-gradient(circle at 18% 20%, rgba(96, 165, 250, .18), transparent 42%);
}

.ambient-glow {
  position: absolute;
  z-index: -1;
  width: 370px;
  height: 370px;
  border-radius: 50%;
  filter: blur(55px);
  pointer-events: none;
  animation: glow-drift 11s ease-in-out infinite alternate;
}

.ambient-glow-one {
  top: -170px;
  right: -120px;
  background: rgba(96, 165, 250, .25);
}

.ambient-glow-two {
  bottom: -210px;
  left: -110px;
  background: rgba(56, 189, 248, .2);
  animation-delay: -5s;
}

.welcome-content {
  position: relative;
  z-index: 2;
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

.music-notes,
.light-waves {
  position: absolute;
  inset: 0;
  z-index: 1;
  pointer-events: none;
  overflow: hidden;
}

.music-note {
  position: absolute;
  bottom: -10%;
  color: rgba(219, 234, 254, .43);
  text-shadow: 0 0 18px rgba(147, 197, 253, .4);
  font-size: 34px;
  animation: note-rise 13s linear infinite;
}

.note-1 {
  left: 8%;
  animation-duration: 12s;
  animation-delay: -3s;
  font-size: 32px;
}

.note-2 {
  left: 23%;
  animation-duration: 16s;
  animation-delay: -9s;
  font-size: 46px;
}

.note-3 {
  left: 39%;
  animation-duration: 14s;
  animation-delay: -6s;
  font-size: 27px;
}

.note-4 {
  left: 56%;
  animation-duration: 17s;
  animation-delay: -13s;
  font-size: 40px;
}

.note-5 {
  left: 73%;
  animation-duration: 15s;
  animation-delay: -2s;
  font-size: 33px;
}

.note-6 {
  left: 87%;
  animation-duration: 18s;
  animation-delay: -11s;
  font-size: 49px;
}

.note-7 {
  left: 94%;
  animation-duration: 13s;
  animation-delay: -7s;
  font-size: 25px;
}

.light-wave {
  position: absolute;
  width: 130%;
  height: 220px;
  left: -15%;
  bottom: -155px;
  border: 2px solid rgba(147, 197, 253, .27);
  border-radius: 50%;
  box-shadow: 0 0 32px rgba(96, 165, 250, .13);
  transform: rotate(-9deg);
  animation: wave-sway 8s ease-in-out infinite alternate;
}

.wave-2 {
  bottom: -118px;
  border-color: rgba(125, 211, 252, .18);
  animation-delay: -3s;
  animation-duration: 10s;
}

.wave-3 {
  bottom: -82px;
  border-color: rgba(219, 234, 254, .12);
  animation-delay: -6s;
  animation-duration: 12s;
}

@keyframes note-rise {
  0% {
    transform: translate3d(0, 0, 0) rotate(-12deg);
    opacity: 0;
  }

  12% {
    opacity: .8;
  }

  85% {
    opacity: .6;
  }

  100% {
    transform: translate3d(55px, -105vh, 0) rotate(20deg);
    opacity: 0;
  }
}

@keyframes wave-sway {
  from {
    transform: translateX(-25px) rotate(-9deg) scale(1);
  }

  to {
    transform: translateX(30px) rotate(-5deg) scale(1.08);
  }
}

@keyframes glow-drift {
  from {
    transform: translate(0, 0);
  }

  to {
    transform: translate(45px, -30px);
  }
}

/* Formulario de recuperación */
.recovery-panel {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 60px 65px;
  box-sizing: border-box;
}

.recovery-content {
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

.recovery-icon {
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

label {
  display: block;
  font-size: 16px;
  font-weight: 600;
  color: #334155;
  margin-bottom: 10px;
}

input {
  width: 100%;
  padding: 18px 20px;
  border: 1px solid #cbd5e1;
  border-radius: 12px;
  font-size: 17px;
  box-sizing: border-box;
  background: white;
  color: #0f172a;
}

input:focus {
  outline: none;
  border-color: #2563eb;
  box-shadow: 0 0 0 3px rgba(37, 99, 235, .12);
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

.recovery-button {
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

.recovery-button:hover:not(:disabled) {
  background: #1d4ed8;
  transform: translateY(-1px);
}

.recovery-button:disabled {
  background: #93c5fd;
  cursor: not-allowed;
}

.back-text {
  text-align: center;
  margin-top: 30px;
  color: #64748b;
  font-size: 16px;
  line-height: 1.7;
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
  .recovery-page {
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

  .recovery-panel {
    padding: 50px 30px;
  }

  .recovery-content {
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

  .recovery-panel {
    padding: 36px 22px;
  }

  .brand h2 {
    font-size: 30px;
  }

  h3 {
    font-size: 25px;
  }

  input {
    font-size: 16px;
  }
}

@media (prefers-reduced-motion: reduce) {

  .music-note,
  .light-wave,
  .ambient-glow {
    animation: none;
  }

  .music-note {
    opacity: .2;
    bottom: 15%;
  }

  .recovery-button {
    transition: none;
  }
}
</style>
