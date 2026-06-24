<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()
const iframeHeight = ref(1100)

const BASE_URL = 'https://api.leadconnectorhq.com/widget/booking/AV9mxK0SEB9vcYGDEKjP'

const calendarUrl = computed(() => {
  try {
    const stored = localStorage.getItem('os_contact')
    if (!stored) return BASE_URL
    const { nombre, email, telefono } = JSON.parse(stored)
    const params = new URLSearchParams()
    if (nombre) params.set('firstName', nombre)
    if (email) params.set('email', email)
    if (telefono) params.set('phone', telefono)
    const qs = params.toString()
    return qs ? `${BASE_URL}?${qs}` : BASE_URL
  } catch {
    return BASE_URL
  }
})

const onMessage = (event: MessageEvent) => {
  if (Array.isArray(event.data) && event.data[0] === 'msgsndr-booking-complete') {
    localStorage.setItem('os_booked_at', String(Date.now()))
    ;(window as any).fbq?.('track', 'CompleteRegistration', {
      content_name: 'diagnostico-agendado',
    })
    router.push('/cita-confirmada')
  }
  if (event.data?.type === 'booking-app' && typeof event.data.height === 'number') {
    iframeHeight.value = event.data.height + 40
  }
}

onMounted(() => {
  window.addEventListener('message', onMessage)

  if (!document.getElementById('ghl-form-embed-script')) {
    const script = document.createElement('script')
    script.id = 'ghl-form-embed-script'
    script.src = 'https://api.leadconnectorhq.com/js/form_embed.js'
    script.type = 'text/javascript'
    document.body.appendChild(script)
  }
})

onUnmounted(() => window.removeEventListener('message', onMessage))
</script>

<template>
  <div class="booking">
    <header class="booking__topbar">
      <h2 class="booking__logo-text">DEKORPAINT</h2>
    </header>

    <main class="booking__main">
      <div class="stepper" aria-label="Paso 2 de 2">
        <div class="stepper__track">
          <div class="stepper__step stepper__step--done">
            <div class="stepper__circle">
              <i class="fa-solid fa-check" aria-hidden="true"></i>
            </div>
            <span class="stepper__label">Video</span>
          </div>
          <div class="stepper__line stepper__line--done"></div>
          <div class="stepper__step stepper__step--active">
            <div class="stepper__circle">2</div>
            <span class="stepper__label">Agenda</span>
          </div>
        </div>
      </div>

      <section class="booking__heading">
        <p class="booking__eyebrow">
          <i class="fa-solid fa-paint-roller" aria-hidden="true"></i>
          Casi listo
        </p>
        <h1 class="booking__title">
          Elige el horario de tu
          <span class="booking__title-accent">diagnóstico técnico avanzado</span>
        </h1>
        <p class="booking__subtitle">
          Una sesión de 30 minutos para analizar tus superficies, identificar patologías y riesgos,
          y definir el recubrimiento técnico adecuado para tu proyecto.
        </p>
      </section>

      <div class="calendar__wrap">
        <iframe
          :src="calendarUrl"
          :style="{ height: iframeHeight + 'px' }"
          title="Agenda tu diagnóstico técnico avanzado con DekorPaint"
          class="calendar__iframe"
          frameborder="0"
          scrolling="no"
          id="AV9mxK0SEB9vcYGDEKjP"
        ></iframe>
      </div>
    </main>

    <footer class="booking__footer">
      <nav class="booking__footer-links" aria-label="Legal">
        <RouterLink to="/politicas-privacidad">Política de Privacidad</RouterLink>
        <RouterLink to="/aviso-legal">Aviso Legal</RouterLink>
      </nav>
      <p class="booking__footer-copy">
        © {{ new Date().getFullYear() }} DekorPaint. Todos los derechos reservados.
      </p>
    </footer>
  </div>
</template>

<style lang="scss" scoped>
@use '@/styles/fonts.modules.scss' as fonts;
@use '@/styles/colorVariables.module.scss' as colors;

.booking {
  min-height: 100vh;
  background: #ffffff;
  color: colors.$OS-DARK;
  display: flex;
  flex-direction: column;
}

.booking__topbar {
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

.booking__logo-text {
  font-family: fonts.$font-principal;
  font-weight: 800;
  font-size: 1.25rem;
  letter-spacing: 0.05em;
  color: colors.$OS-NAVY;
  margin: 0;
}

.booking__main {
  flex: 1;
  max-width: 720px;
  margin: 0 auto;
  padding: 2rem 1.5rem 3rem;
  width: 100%;
}

.stepper {
  display: flex;
  justify-content: center;
  margin-bottom: 2rem;

  &__track {
    display: flex;
    align-items: center;
    gap: 0;
  }
  &__step {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 0.35rem;
  }

  &__circle {
    width: 36px;
    height: 36px;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    font-family: fonts.$font-interface;
    font-size: 0.85rem;
    font-weight: 800;
    border: 2px solid #d0dbe8;
    color: #d0dbe8;
    transition: all 0.3s ease;

    .stepper__step--done & {
      background: colors.$OS-BLUE;
      border-color: colors.$OS-BLUE;
      color: #ffffff;
    }
    .stepper__step--active & {
      background: colors.$OS-RED;
      border-color: colors.$OS-RED;
      color: #ffffff;
    }
  }

  &__label {
    font-family: fonts.$font-interface;
    font-size: 0.72rem;
    font-weight: 600;
    letter-spacing: 0.04em;
    color: #b0c0d5;
    .stepper__step--done & {
      color: colors.$OS-BLUE;
    }
    .stepper__step--active & {
      color: colors.$OS-DARK;
    }
  }

  &__line {
    width: 60px;
    height: 2px;
    background: #e0eaf5;
    border-radius: 2px;
    margin: 0 0.5rem;
    margin-bottom: 1.1rem;
    &--done {
      background: colors.$OS-BLUE;
    }
  }
}

.booking__eyebrow {
  display: inline-flex;
  align-items: center;
  gap: 0.4rem;
  font-family: fonts.$font-interface;
  font-size: 0.76rem;
  font-weight: 700;
  letter-spacing: 0.06em;
  text-transform: uppercase;
  color: colors.$OS-NAVY;
  margin: 0 0 0.75rem;
  i {
    font-size: 0.75rem;
  }
}

.booking__heading {
  margin-bottom: 1.75rem;
}

.booking__title {
  @include fonts.heading-font(800);
  font-size: clamp(1.7rem, 4vw, 2.4rem);
  color: colors.$OS-DARK;
  margin: 0 0 0.6rem;
  letter-spacing: -0.025em;
  line-height: 1.2;
  &-accent {
    color: colors.$OS-RED;
  }
}

.booking__subtitle {
  font-size: 0.93rem;
  color: #4a5f7a;
  line-height: 1.6;
  margin: 0;
}

.calendar__wrap {
  border-radius: 16px;
  overflow: hidden;
  border: 1px solid #e4edf7;
  box-shadow: 0 4px 24px rgba(0, 82, 165, 0.07);
}

.calendar__iframe {
  width: 100%;
  display: block;
  min-height: 600px;
}

.booking__footer {
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
