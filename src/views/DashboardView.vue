
<script setup>
import { computed, ref } from "vue";
import { useRouter } from "vue-router";
import { authState, logoutSession } from "../services/authService";

const router = useRouter();
const cerrandoSesion = ref(false);
const errorLogout = ref("");

const usuario = computed(() => authState.user);

const nombreRol = computed(() => {
  const roles = {
    attendee: "Asistente",
    organizer: "Organizador",
    admin: "Administrador"
  };

  return roles[usuario.value?.role] || "Usuario";
});

const cerrarSesion = async () => {
  cerrandoSesion.value = true;
  errorLogout.value = "";

  try {
    const resultado = await logoutSession();

    if (resultado) {
      await router.replace("/login");
    } else {
      errorLogout.value =
        "No se pudo cerrar la sesión. Inténtalo nuevamente.";
    }
  } catch (error) {
    errorLogout.value =
      "Ocurrió un error al cerrar la sesión.";
  } finally {
    cerrandoSesion.value = false;
  }
};
</script>

<template>
  <main class="dashboard-page">

    <!-- BARRA SUPERIOR -->
    <header class="navbar">
      <div class="navbar-content">
        <div class="logo">✦ EventHub</div>

        <div class="navbar-actions">
          <span class="navbar-user">
            {{ usuario?.name || "Usuario" }}
          </span>

          <button
            class="logout-button"
            type="button"
            :disabled="cerrandoSesion"
            @click="cerrarSesion"
          >
            {{ cerrandoSesion ? "Cerrando..." : "Cerrar sesión" }}
          </button>
        </div>
      </div>
    </header>

    <!-- CONTENIDO PRINCIPAL -->
    <div class="dashboard-content">

      <section class="welcome-section">
        <span class="section-label">PANEL PRINCIPAL</span>

        <h1>
          ¡Bienvenido{{ usuario ? `, ${usuario.name}` : "" }}! 🎉
        </h1>

        <p>
          Consulta la información de tu cuenta y disfruta
          de tu experiencia en EventHub.
        </p>
      </section>

      <p
        v-if="errorLogout"
        class="logout-error"
        role="alert"
      >
        {{ errorLogout }}
      </p>

      <!-- TARJETAS -->
      <section class="cards-grid">

        <!-- MI CUENTA -->
        <article class="info-card">
          <div class="card-heading">
            <div class="card-icon" aria-hidden="true">
              <svg
                viewBox="0 0 24 24"
                fill="none"
                stroke="currentColor"
                stroke-width="1.8"
                stroke-linecap="round"
                stroke-linejoin="round"
              >
                <circle cx="12" cy="8" r="4" />
                <path d="M4 21v-2a8 8 0 0 1 16 0v2" />
              </svg>
            </div>

            <div>
              <span class="card-label">PERFIL PERSONAL</span>
              <h2>Mi cuenta</h2>
            </div>
          </div>

          <div class="info-row">
            <span>Nombre</span>
            <strong>{{ usuario?.name || "No disponible" }}</strong>
          </div>

          <div class="info-row">
            <span>Correo electrónico</span>
            <strong>{{ usuario?.email || "No disponible" }}</strong>
          </div>
        </article>

        <!-- INFORMACIÓN DE ACCESO -->
        <article class="info-card">
          <div class="card-heading">
            <div class="card-icon" aria-hidden="true">
              <svg
                viewBox="0 0 24 24"
                fill="none"
                stroke="currentColor"
                stroke-width="1.8"
                stroke-linecap="round"
                stroke-linejoin="round"
              >
                <path
                  d="M12 22s8-4 8-10V5l-8-3-8 3v7c0 6 8 10 8 10Z"
                />
                <path d="m9 12 2 2 4-4" />
              </svg>
            </div>

            <div>
              <span class="card-label">SEGURIDAD</span>
              <h2>Información de acceso</h2>
            </div>
          </div>

          <div class="info-row">
            <span>Rol de usuario</span>
            <strong>{{ nombreRol }}</strong>
          </div>

          <div class="info-row">
            <span>Estado del correo</span>

            <strong
              class="status"
              :class="usuario?.isEmailVerified ? 'verified' : 'pending'"
            >
              <span class="status-dot"></span>
              {{
                usuario?.isEmailVerified
                  ? "Verificado"
                  : "Pendiente de verificación"
              }}
            </strong>
          </div>
        </article>

      </section>
    </div>
  </main>
</template>

<style scoped>
* {
  box-sizing: border-box;
}

.dashboard-page {
  min-height: 100vh;
  background: #f3f6fc;
  font-family: Arial, sans-serif;
  color: #0f172a;
}

/* BARRA SUPERIOR */
.navbar {
  background: linear-gradient(110deg, #0f172a, #1e3a8a);
  padding: 20px 36px;
  color: white;
}

.navbar-content {
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 20px;
}

.logo {
  font-size: 30px;
  font-weight: 700;
  color: white;
  white-space: nowrap;
}

.navbar-actions {
  display: flex;
  align-items: center;
  justify-content: flex-end;
  gap: 22px;
  margin-left: auto;
}

.navbar-user {
  font-size: 16px;
  font-weight: 500;
  overflow-wrap: anywhere;
}

.logout-button {
  padding: 12px 22px;
  background: #ffffff;
  color: #1e3a8a;
  border: none;
  border-radius: 10px;
  font-size: 15px;
  font-weight: 700;
  cursor: pointer;
  white-space: nowrap;
  transition: background 0.2s, transform 0.2s;
}

.logout-button:hover:not(:disabled) {
  background: #dbeafe;
  transform: translateY(-2px);
}

.logout-button:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

/* CONTENIDO */
.dashboard-content {
  width: 100%;
  max-width: 1250px;
  margin: 0 auto;
  padding: 60px 36px;
}

.welcome-section {
  margin-bottom: 42px;
}

.section-label {
  color: #2563eb;
  font-size: 13px;
  font-weight: 700;
  letter-spacing: 2.5px;
}

.welcome-section h1 {
  font-size: clamp(30px, 3.5vw, 46px);
  color: #0f172a;
  margin: 18px 0;
  letter-spacing: -0.7px;
}

.welcome-section p {
  font-size: 18px;
  line-height: 1.7;
  color: #64748b;
  max-width: 780px;
}

/* TARJETAS MODERNAS */
.cards-grid {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 28px;
}

.info-card {
  background: #ffffff;
  border: 1px solid #e2e8f0;
  border-radius: 20px;
  padding: 34px;
  min-width: 0;
  box-shadow: 0 10px 30px rgba(15, 23, 42, 0.05);
  transition:
    transform 0.3s ease,
    box-shadow 0.3s ease,
    border-color 0.3s ease;
}

.info-card:hover {
  transform: translateY(-6px);
  border-color: #bfdbfe;
  box-shadow: 0 20px 45px rgba(37, 99, 235, 0.12);
}

.card-heading {
  display: flex;
  align-items: center;
  gap: 18px;
  margin-bottom: 28px;
}

.card-icon {
  width: 58px;
  height: 58px;
  flex-shrink: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 16px;
  background: linear-gradient(135deg, #eff6ff, #dbeafe);
  color: #2563eb;
}

.card-icon svg {
  width: 30px;
  height: 30px;
}

.card-label {
  display: block;
  color: #64748b;
  font-size: 11px;
  font-weight: 700;
  letter-spacing: 1.5px;
  margin-bottom: 7px;
}

.info-card h2 {
  font-size: 22px;
  margin: 0;
  color: #0f172a;
}

.info-row {
  display: flex;
  flex-direction: column;
  gap: 8px;
  padding: 16px 0;
  border-bottom: 1px solid #e2e8f0;
}

.info-row:last-child {
  border-bottom: none;
}

.info-row span {
  font-size: 14px;
  color: #64748b;
}

.info-row strong {
  font-size: 16px;
  font-weight: 600;
  color: #0f172a;
  overflow-wrap: anywhere;
}

/* ESTADOS */
.status {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  width: fit-content;
}

.status .status-dot {
  width: 9px;
  height: 9px;
  border-radius: 50%;
  flex-shrink: 0;
}

.info-row .verified {
  color: #15803d;
}

.verified .status-dot {
  background: #22c55e;
}

.info-row .pending {
  color: #b45309;
}

.pending .status-dot {
  background: #f59e0b;
}

/* ERRORES */
.logout-error {
  color: #b91c1c;
  background: #fef2f2;
  border: 1px solid #fecaca;
  padding: 14px;
  border-radius: 10px;
  margin-bottom: 24px;
}

/* TABLETAS */
@media (max-width: 768px) {
  .navbar {
    padding: 18px 20px;
  }

  .logo {
    font-size: 25px;
  }

  .navbar-actions {
    gap: 12px;
  }

  .navbar-user {
    font-size: 14px;
  }

  .dashboard-content {
    padding: 38px 20px;
  }

  .cards-grid {
    grid-template-columns: 1fr;
  }

  .info-card {
    padding: 26px;
  }

  .welcome-section p {
    font-size: 16px;
  }
}

/* CELULARES */
@media (max-width: 480px) {
  .navbar-content {
    flex-wrap: wrap;
  }

  .navbar-actions {
    width: 100%;
    justify-content: space-between;
    margin-left: 0;
  }

  .logout-button {
    padding: 10px 14px;
    font-size: 13px;
  }

  .card-heading {
    gap: 14px;
  }

  .card-icon {
    width: 50px;
    height: 50px;
  }

  .info-card h2 {
    font-size: 19px;
  }
}

/* ACCESIBILIDAD */
@media (prefers-reduced-motion: reduce) {
  .info-card,
  .logout-button {
    transition: none;
  }

  .info-card:hover,
  .logout-button:hover {
    transform: none;
  }
}
</style>
