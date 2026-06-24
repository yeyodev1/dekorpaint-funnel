<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'

const COOLDOWN_MS = 48 * 60 * 60 * 1000

const hoursLeft = ref(0)

const contactName = computed(() => {
  try {
    const stored = localStorage.getItem('os_contact')
    if (!stored) return ''
    return JSON.parse(stored).nombre ?? ''
  } catch {
    return ''
  }
})

onMounted(() => {
  const osDisqAt = localStorage.getItem('os_disq_at')
  if (osDisqAt) {
    const elapsed = Date.now() - Number(osDisqAt)
    const remaining = COOLDOWN_MS - elapsed
    if (remaining > 0) {
      hoursLeft.value = Math.ceil(remaining / (60 * 60 * 1000))
    }
  }
})
</script>

<template>
  <div class="nospace">
    <header class="nospace__topbar">
      <h2 class="nospace__logo-text">DEKORPAINT</h2>
    </header>

    <main class="nospace__main">
      <div v-if="hoursLeft > 0" class="nospace__cooldown" role="alert">
        <i class="fa-solid fa-clock" aria-hidden="true"></i>
        <span>
          <template v-if="contactName">{{ contactName }}, podrás</template>
          <template v-else>Podrás</template>
          volver y solicitar un nuevo diagnóstico técnico avanzado en
          <strong>{{ hoursLeft }} hora{{ hoursLeft !== 1 ? 's' : '' }}</strong>
        </span>
      </div>

      <div class="nospace__card">
        <div class="nospace__icon-wrap" aria-hidden="true">
          <i class="fa-solid fa-calendar-xmark nospace__icon"></i>
        </div>

        <h1 class="nospace__title">Cupos completos para diagnóstico técnico avanzado</h1>

        <p class="nospace__body">
          Nuestro equipo de ingeniería y aplicadores certificados tiene una capacidad
          deliberadamente limitada para garantizar la calidad de cada diagnóstico. En este momento
          los
          <strong>5 proyectos de gran escala mensuales</strong> de DekorPaint están asignados.
        </p>

        <p class="nospace__body">
          Esto nos permite dedicarle el tiempo, la especificación y el seguimiento que cada
          superficie industrial o residencia premium merece. Cuando se libere un espacio, podrás
          agendar tu Diagnóstico Técnico Avanzado sin costo.
        </p>

        <div class="nospace__divider" aria-hidden="true"></div>

        <h2 class="nospace__subtitle">Mientras tanto</h2>
        <p class="nospace__body">
          Aprovecha estas horas para preparar fotos claras de las superficies afectadas, registros
          de mantenimiento previo y una lista de preguntas específicas que quieras resolver con
          Alejandro Bravo en tu sesión.
        </p>
      </div>

      <p class="nospace__footer-note">
        <i class="fa-solid fa-circle-info" aria-hidden="true"></i>
        DekorPaint trabaja con industria exigente y residencias premium. No realizamos parches
        baratos ni aplicaciones sin especificación técnica previa.
      </p>
    </main>

    <footer class="nospace__footer">
      <nav class="nospace__footer-links" aria-label="Legal">
        <RouterLink to="/politicas-privacidad">Política de Privacidad</RouterLink>
        <RouterLink to="/aviso-legal">Aviso Legal</RouterLink>
      </nav>
      <p class="nospace__footer-copy">
        © {{ new Date().getFullYear() }} DekorPaint. Todos los derechos reservados.
      </p>
    </footer>
  </div>
</template>

<style lang="scss" scoped>
@use '@/styles/fonts.modules.scss' as fonts;
@use '@/styles/colorVariables.module.scss' as colors;

.nospace {
  min-height: 100vh;
  background: #ffffff;
  color: colors.$OS-DARK;
  display: flex;
  flex-direction: column;
}

.nospace__topbar {
  background: #ffffff;
  border-bottom: 1px solid #e8edf5;
  padding: 0.9rem 1.5rem;
  display: flex;
  justify-content: center;
  position: sticky;
  top: 0;
  z-index: 100;
  box-shadow: 0 1px 8px rgba(0, 0, 0, 0.05);
}

.nospace__logo-text {
  font-family: fonts.$font-principal;
  font-weight: 800;
  font-size: 1.25rem;
  letter-spacing: 0.05em;
  color: colors.$OS-NAVY;
  margin: 0;
}

.nospace__main {
  flex: 1;
  max-width: 560px;
  margin: 0 auto;
  padding: 3rem 1.5rem;
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 2rem;
}

.nospace__cooldown {
  display: inline-flex;
  align-items: center;
  gap: 0.6rem;
  background: #f0f6ff;
  border: 1px solid rgba(colors.$OS-BLUE, 0.2);
  border-radius: 999px;
  padding: 0.6rem 1.25rem;
  font-family: fonts.$font-interface;
  font-size: 0.82rem;
  color: colors.$OS-NAVY;
  i {
    color: colors.$OS-BLUE;
    flex-shrink: 0;
  }
  strong {
    font-weight: 800;
  }
}

.nospace__card {
  background: #f9fbff;
  border: 1px solid #e4edf7;
  border-radius: 20px;
  padding: 2.5rem 2rem;
  text-align: center;
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.04);
}

.nospace__icon-wrap {
  width: 64px;
  height: 64px;
  border-radius: 50%;
  background: rgba(colors.$OS-NAVY, 0.06);
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 0 auto 1.25rem;
}

.nospace__icon {
  font-size: 1.8rem;
  color: colors.$OS-NAVY;
}

.nospace__title {
  @include fonts.heading-font(800);
  font-size: 1.6rem;
  color: colors.$OS-DARK;
  margin: 0 0 1rem;
  line-height: 1.2;
}

.nospace__body {
  font-size: 0.92rem;
  color: #4a5f7a;
  line-height: 1.65;
  margin: 0 0 1rem;

  &:last-of-type {
    margin-bottom: 0;
  }
  strong {
    color: colors.$OS-DARK;
    font-weight: 700;
  }
}

.nospace__subtitle {
  @include fonts.heading-font(800);
  font-size: 1.2rem;
  color: colors.$OS-DARK;
  margin: 0 0 0.75rem;
}

.nospace__divider {
  width: 48px;
  height: 3px;
  background: colors.$OS-BLUE;
  border-radius: 99px;
  margin: 1.5rem auto;
}

.nospace__footer-note {
  display: flex;
  gap: 0.5rem;
  align-items: flex-start;
  font-size: 0.76rem;
  color: #a0b0c5;
  line-height: 1.55;
  margin: 0;
  max-width: 420px;
  text-align: left;
  i {
    font-size: 0.8rem;
    flex-shrink: 0;
    margin-top: 1px;
    color: #c0d0e0;
  }
}

.nospace__footer {
  padding: 1.5rem;
  border-top: 1px solid #f0f4fb;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.5rem;
  text-align: center;

  &-links {
    display: flex;
    gap: 1.5rem;
    a {
      font-size: 0.76rem;
      color: #b0c0d5;
      text-decoration: none;
      &:hover {
        color: colors.$OS-NAVY;
      }
    }
  }
  &-copy {
    font-size: 0.72rem;
    color: #c8d8ed;
    margin: 0;
  }
}
</style>
