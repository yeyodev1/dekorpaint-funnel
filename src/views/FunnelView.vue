<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'
import { useRouter } from 'vue-router'
import RegistrationModal from '@/components/RegistrationModal.vue'
import { captureFbParams } from '@/utils/fbclid'
import dekorpaintLogo from '@/assets/logos/image.png'

const router = useRouter()
const modalOpen = ref(false)
const IS_DEV = window.location.hostname === 'localhost'

const openModal = () => {
  if (!IS_DEV) {
    const disqAt = localStorage.getItem('os_disq_at')
    if (disqAt && Date.now() - Number(disqAt) < 48 * 60 * 60 * 1000) {
      router.push('/sin-espacio')
      return
    }
  }
  ;(window as any).fbq?.('track', 'CompleteRegistration', { content_name: 'cta-abrir-registro' })
  modalOpen.value = true
}

const stats = [
  {
    icon: 'fa-solid fa-chart-line',
    number: '70%+',
    text: 'Reducción promedio en costos de mantenimiento de pisos y recubrimientos industriales',
  },
  {
    icon: 'fa-solid fa-calendar-check',
    number: '20+',
    text: 'Años de durabilidad en recubrimientos técnicos aplicados con el método certificado',
  },
  {
    icon: 'fa-solid fa-award',
    number: '100%',
    text: 'De proyectos industriales aprobados en auditorías regulatorias y de seguridad',
  },
]

const before = [
  'Pisos industriales que se desgastan y descascaran — causando paradas de producción, riesgo de accidentes y multas por incumplimiento de normas',
  'Filtraciones y humedad que corren estructuras, equipos y acabados — generando reparaciones mayores que nadie presupuestó',
  'Pintura arquitectónica de bajo performance que se decolora, cuartea y exige repintes costosos cada año',
  'Fallas en señalización de seguridad y demarcación — exponiendo a la empresa a sanciones y a personas a riesgos evitables',
  'Contratistas que compiten solo por precio, sin preparación del sustrato ni trazabilidad de materiales certificados',
]

const pillars = [
  {
    num: '01',
    icon: 'fa-solid fa-trowel-bricks',
    title: 'Preparación Mecánica del Sustrato y Perfil de Anclaje',
    body: 'No aplicamos sobre superficies mal preparadas. Realizamos shot blasting, diamantado o sandblasting según norma para crear el perfil de anclaje que exige el sistema. Esto es lo que determina si el recubrimiento dura 2 años o más de 20.',
  },
  {
    num: '02',
    icon: 'fa-solid fa-flask',
    title: 'Dosificación de Polímeros Certificados de Alta Performance',
    body: 'Trabajamos con epoxias, poliuretanos cementicios y poliureas de grado industrial, dosificados según ficha técnica y condiciones de uso. Sin mezclas a ojo, sin recetas genéricas: cada proyecto lleva química específica para su carga, tráfico y exposición.',
  },
  {
    num: '03',
    icon: 'fa-solid fa-shield-halved',
    title: 'Blindaje Regulatorio y Señalización de Seguridad',
    body: 'Entregamos trazabilidad completa de materiales, especificaciones técnicas y cumplimiento normativo. Además, ejecutamos demarcación y señalización de seguridad industrial para que tu planta pase auditorías sin contratiempos.',
  },
]

const testimonials = [
  {
    quote:
      'Nuestra planta embotelladora tenía pisos industriales deteriorados que generaban paradas constantes y riesgo de accidentes. DekorPaint aplicó su metodología técnica y, a 18 meses, el piso luce como el primer día. Pasamos la auditoría de seguridad sin una sola observación y reducimos los costos de mantenimiento de pisos en más de un 70%.',
    author: 'Ing. Roberto Vásquez',
    role: 'Gerente de Planta — Embotelladora Multinacional',
  },
  {
    quote:
      'Pensábamos que filtraciones en bodegas eran normales y que había que convivir con ellas. El equipo de Alejandro nos demostró que una impermeabilización técnica bien ejecutada elimina el problema de raíz. Recuperamos espacio, protegimos inventario y dejamos de invertir en parches temporales.',
    author: 'Carla Mendieta',
    role: 'Directora de Operaciones — Cadena de Restaurantes Global',
  },
  {
    quote:
      'Al principio dudé porque recibí cotizaciones mucho más baratas para pintar el showroom de vehículos premium. Pero DekorPaint no pinta: aplica recubrimientos técnicos. El acabado, la resistencia al tráfico y la imagen de marca son otro nivel. Vale cada dólar de diferencia.',
    author: 'Andrés Peñafiel',
    role: 'Propietario — Concesionario de Vehículos Premium',
  },
]

const activeTestimonial = ref(0)

const BLOCK_HOURS = 6
const hours = ref('00')
const minutes = ref('00')
const seconds = ref('00')
let interval: ReturnType<typeof setInterval>

const computeRemaining = () => {
  const now = new Date()
  const blockStartHour = Math.floor(now.getHours() / BLOCK_HOURS) * BLOCK_HOURS
  const blockStart = new Date(now)
  blockStart.setHours(blockStartHour, 0, 0, 0)
  const blockEndMs = blockStart.getTime() + BLOCK_HOURS * 3600 * 1000
  return Math.max(0, Math.floor((blockEndMs - now.getTime()) / 1000))
}

const tick = () => {
  const total = computeRemaining()
  hours.value = String(Math.floor(total / 3600)).padStart(2, '0')
  minutes.value = String(Math.floor((total % 3600) / 60)).padStart(2, '0')
  seconds.value = String(total % 60).padStart(2, '0')
}

onMounted(() => {
  captureFbParams()
  sessionStorage.setItem('alu_page_entry', String(Date.now()))
  tick()
  interval = setInterval(tick, 1000)
  startProofRotation()
})

onUnmounted(() => {
  clearInterval(interval)
  stopProofRotation()
})

type Proof = { who: string; where: string; minutesAgo: number }

const PROOFS: Proof[] = [
  { who: 'Embotelladora Multinacional', where: 'Guayaquil', minutesAgo: 3 },
  { who: 'Planta de Procesamiento de Alimentos', where: 'Quito', minutesAgo: 8 },
  { who: 'Concesionario de Vehículos Premium', where: 'Manta', minutesAgo: 14 },
  { who: 'Cadena Global de Restaurantes', where: 'Guayaquil', minutesAgo: 19 },
  { who: 'Laboratorio Clínico', where: 'Cuenca', minutesAgo: 25 },
  { who: 'Bodega Logística', where: 'Quito', minutesAgo: 31 },
  { who: 'Planta Industrial', where: 'Esmeraldas', minutesAgo: 38 },
  { who: 'Residencia Exclusiva', where: 'Guayaquil', minutesAgo: 44 },
  { who: 'Clínica Privada', where: 'Quito', minutesAgo: 50 },
  { who: 'Centro Comercial', where: 'Guayaquil', minutesAgo: 57 },
]

const proofVisible = ref(false)
const proofIndex = ref(0)
const currentProof = ref<Proof | null>(null)
let proofShowTimer: ReturnType<typeof setTimeout> | null = null
let proofHideTimer: ReturnType<typeof setTimeout> | null = null
let proofDismissed = false

const SHOW_AFTER_MS = 3000
const VISIBLE_FOR_MS = 5000
const GAP_BETWEEN_MS = 2000

const showNextProof = () => {
  if (proofDismissed) return
  currentProof.value = PROOFS[proofIndex.value % PROOFS.length]
  proofIndex.value++
  proofVisible.value = true
  proofHideTimer = setTimeout(() => {
    proofVisible.value = false
    proofShowTimer = setTimeout(showNextProof, GAP_BETWEEN_MS)
  }, VISIBLE_FOR_MS)
}

const startProofRotation = () => {
  proofShowTimer = setTimeout(showNextProof, SHOW_AFTER_MS)
}

const stopProofRotation = () => {
  if (proofShowTimer) clearTimeout(proofShowTimer)
  if (proofHideTimer) clearTimeout(proofHideTimer)
  proofShowTimer = null
  proofHideTimer = null
}

const dismissProof = () => {
  proofDismissed = true
  proofVisible.value = false
  stopProofRotation()
}
</script>

<template>
  <div class="funnel">
    <header class="funnel__topbar">
      <img :src="dekorpaintLogo" alt="DekorPaint" class="funnel__logo-img" />
      <p class="funnel__logo-sub">Recubrimientos Técnicos Avanzados</p>
    </header>

    <div class="funnel__urgency" role="banner">
      <div class="funnel__urgency-info">
        <span class="funnel__urgency-dot" aria-hidden="true" />
        <i class="fa-solid fa-bolt funnel__urgency-icon" aria-hidden="true"></i>
        <span class="funnel__urgency-text"
          >CUPOS PARA <strong>DIAGNÓSTICO TÉCNICO AVANZADO GRATIS</strong> — Cierran en:</span
        >
        <div class="funnel__timer" aria-live="polite" aria-label="Tiempo restante">
          <span class="funnel__timer-block"
            ><strong>{{ hours }}</strong
            ><small>h</small></span
          >
          <span class="funnel__timer-sep" aria-hidden="true">:</span>
          <span class="funnel__timer-block"
            ><strong>{{ minutes }}</strong
            ><small>m</small></span
          >
          <span class="funnel__timer-sep" aria-hidden="true">:</span>
          <span class="funnel__timer-block"
            ><strong>{{ seconds }}</strong
            ><small>s</small></span
          >
        </div>
      </div>
      <button
        type="button"
        class="funnel__urgency-cta"
        aria-label="Agendar diagnóstico técnico"
        @click="openModal()"
      >
        AGENDAR MI DIAGNÓSTICO
        <span aria-hidden="true">→</span>
      </button>
    </div>

    <Transition name="proof-fade">
      <div
        v-if="proofVisible && currentProof"
        class="funnel__proof"
        role="status"
        aria-live="polite"
      >
        <div class="funnel__proof-icon" aria-hidden="true">
          <i class="fa-solid fa-circle-check"></i>
        </div>
        <div class="funnel__proof-body">
          <p class="funnel__proof-title">
            <strong>{{ currentProof.who }}</strong>
            <span>{{ currentProof.where }}</span>
          </p>
          <p class="funnel__proof-text">
            Acaba de agendar un <strong>Diagnóstico Técnico Avanzado</strong>
          </p>
          <p class="funnel__proof-meta">
            <i class="fa-solid fa-clock" aria-hidden="true"></i>
            Hace {{ currentProof.minutesAgo }} min
          </p>
        </div>
        <button
          type="button"
          class="funnel__proof-close"
          aria-label="Cerrar notificación"
          @click="dismissProof"
        >
          <i class="fa-solid fa-xmark" aria-hidden="true"></i>
        </button>
      </div>
    </Transition>

    <section class="funnel__hero" aria-labelledby="funnel-headline">
      <div class="funnel__container">
        <p class="funnel__eyebrow">
          <i class="fa-solid fa-paint-roller" aria-hidden="true"></i>
          Recubrimientos Técnicos Avanzados
        </p>

        <h1 id="funnel-headline" class="funnel__headline">
          Protege tus pisos, estructuras y activos con recubrimientos técnicos que duran décadas y
          <span class="funnel__headline-accent">aprueban cualquier auditoría regulatoria</span>
        </h1>

        <p class="funnel__hero-sub">
          Descubre cómo DekorPaint aplica su Método de Recubrimiento Técnico Avanzado en plantas
          industriales, bodegas, laboratorios, clínicas, cadenas globales y residencias exclusivas —
          eliminando paradas no programadas, repintes constantes y riesgos normativos que erosionan
          tu operación y tu patrimonio.
        </p>

        <ul class="funnel__benefits" role="list">
          <li class="funnel__benefit">
            <i class="fa-solid fa-check-circle" aria-hidden="true"></i>
            Sin paradas de producción por pisos industriales deteriorados
          </li>
          <li class="funnel__benefit">
            <i class="fa-solid fa-check-circle" aria-hidden="true"></i>
            Sin filtraciones ni humedad que corran estructuras y equipos
          </li>
          <li class="funnel__benefit">
            <i class="fa-solid fa-check-circle" aria-hidden="true"></i>
            Sin repintes anuales ni acabados que se decoloren y cuarteen
          </li>
          <li class="funnel__benefit">
            <i class="fa-solid fa-check-circle" aria-hidden="true"></i>
            Con certificación, trazabilidad de materiales y cumplimiento normativo real
          </li>
        </ul>

        <div class="funnel__urgency-callout" role="note">
          <i class="fa-solid fa-fire" aria-hidden="true"></i>
          <span
            >Para
            <strong
              >directores de planta, gerentes de mantenimiento y propietarios de activos
              premium</strong
            >
            — no para quienes eligen el precio más bajo sobre la calidad certificada.</span
          >
        </div>

        <div class="funnel__vsl-wrap">
          <div
            class="funnel__vsl"
            @click="openModal()"
            role="button"
            aria-label="Ver video"
            tabindex="0"
          >
            <div class="funnel__vsl-bg">
              <img
                src="https://fast.wistia.com/embed/medias/e3o2zxds45/swatch"
                class="funnel__vsl-thumb"
                alt=""
              />
              <div class="funnel__vsl-blur-overlay"></div>
            </div>
            <div class="funnel__vsl-overlay">
              <div class="funnel__vsl-play">
                <i class="fa-solid fa-play" aria-hidden="true"></i>
              </div>
              <p class="funnel__vsl-caption">
                Mira el video y descubre cómo el Método de Recubrimiento Técnico Avanzado protege tu
                infraestructura
              </p>
            </div>
          </div>
        </div>

        <div class="funnel__cta-wrap">
          <button class="funnel__cta-btn" @click="openModal()">
            <i class="fa-solid fa-calendar-check" aria-hidden="true"></i>
            AGENDAR MI DIAGNÓSTICO TÉCNICO AVANZADO GRATIS
          </button>
          <p class="funnel__cta-sub">
            <i class="fa-solid fa-lock" aria-hidden="true"></i>
            100% gratuito &nbsp;·&nbsp; Sin compromiso &nbsp;·&nbsp; Cupos limitados
          </p>
        </div>
      </div>
    </section>

    <section class="funnel__stats" aria-label="Resultados comprobados">
      <div class="funnel__container">
        <p class="funnel__section-label funnel__section-label--light">
          Resultados reales — clientes reales
        </p>
        <div class="funnel__stats-grid">
          <div v-for="stat in stats" :key="stat.number" class="funnel__stat">
            <div class="funnel__stat-icon" aria-hidden="true">
              <i :class="stat.icon"></i>
            </div>
            <strong class="funnel__stat-number">{{ stat.number }}</strong>
            <p class="funnel__stat-text">{{ stat.text }}</p>
          </div>
        </div>
      </div>
    </section>

    <section class="funnel__problem" aria-labelledby="problem-heading">
      <div class="funnel__container">
        <p class="funnel__section-label">¿Te identificas con esto?</p>
        <h2 id="problem-heading" class="funnel__section-title">
          El mito de que el deterioro de pisos, filtraciones y repintes son "normales"
        </h2>
        <p class="funnel__problem-intro">
          Contratar al aplicador más barato no es un ahorro — es un pasivo que pagas en paradas de
          producción, reparaciones mayores, multas normativas y pérdida de imagen. Esto es lo que
          realmente estás pagando:
        </p>
        <div class="funnel__problem-grid">
          <div v-for="(item, i) in before" :key="i" class="funnel__problem-item">
            <i class="fa-solid fa-triangle-exclamation funnel__problem-icon" aria-hidden="true"></i>
            <div>
              <strong>{{ item.split(' — ')[0] || item }}</strong>
              <p>{{ item.includes(' — ') ? item.split(' — ')[1] : '' }}</p>
            </div>
          </div>
        </div>
      </div>
    </section>

    <section class="funnel__method" aria-labelledby="method-heading">
      <div class="funnel__container">
        <p class="funnel__section-label">El Método de Recubrimiento Técnico Avanzado</p>
        <h2 id="method-heading" class="funnel__section-title">
          Tres pilares que separan un recubrimiento ordinario de uno técnico
        </h2>

        <div class="funnel__pillar-card">
          <div class="funnel__pillar-num" aria-hidden="true">01</div>
          <div class="funnel__pillar-content">
            <div class="funnel__pillar-icon" aria-hidden="true">
              <i class="fa-solid fa-trowel-bricks"></i>
            </div>
            <h3 class="funnel__pillar-title">
              Preparación Mecánica del Sustrato y Perfil de Anclaje
            </h3>
            <p class="funnel__pillar-body">
              No aplicamos sobre superficies mal preparadas. Realizamos shot blasting, diamantado o
              sandblasting según norma para crear el perfil de anclaje que exige el sistema. Esto es
              lo que determina si el recubrimiento dura 2 años o más de 20.
            </p>
          </div>
        </div>

        <div class="funnel__pillar-card">
          <div class="funnel__pillar-num" aria-hidden="true">02</div>
          <div class="funnel__pillar-content">
            <div class="funnel__pillar-icon" aria-hidden="true">
              <i class="fa-solid fa-flask"></i>
            </div>
            <h3 class="funnel__pillar-title">
              Dosificación de Polímeros Certificados de Alta Performance
            </h3>
            <p class="funnel__pillar-body">
              Trabajamos con epoxias, poliuretanos cementicios y poliureas de grado industrial,
              dosificados según ficha técnica y condiciones de uso. Sin mezclas a ojo, sin recetas
              genéricas: cada proyecto lleva química específica para su carga, tráfico y exposición.
            </p>
          </div>
        </div>

        <div class="funnel__pillar-card">
          <div class="funnel__pillar-num" aria-hidden="true">03</div>
          <div class="funnel__pillar-content">
            <div class="funnel__pillar-icon" aria-hidden="true">
              <i class="fa-solid fa-shield-halved"></i>
            </div>
            <h3 class="funnel__pillar-title">Blindaje Regulatorio y Señalización de Seguridad</h3>
            <p class="funnel__pillar-body">
              Entregamos trazabilidad completa de materiales, especificaciones técnicas y
              cumplimiento normativo. Además, ejecutamos demarcación y señalización de seguridad
              industrial para que tu planta pase auditorías sin contratiempos.
            </p>
          </div>
        </div>
      </div>
    </section>

    <section class="funnel__testimonials" aria-labelledby="testimonials-heading">
      <div class="funnel__container">
        <p class="funnel__section-label">Casos de éxito</p>
        <h2 id="testimonials-heading" class="funnel__section-title">
          Lo que dicen quienes ya confiaron en DekorPaint
        </h2>

        <div class="funnel__test-carousel">
          <div
            v-for="(t, i) in testimonials"
            :key="i"
            class="funnel__test-card"
            :class="{ active: activeTestimonial === i }"
          >
            <i class="fa-solid fa-quote-left funnel__test-quote" aria-hidden="true"></i>
            <blockquote class="funnel__test-text">{{ t.quote }}</blockquote>
            <footer class="funnel__test-author">
              <div class="funnel__test-avatar" aria-hidden="true">
                <i class="fa-solid fa-user-tie"></i>
              </div>
              <div>
                <strong>{{ t.author }}</strong>
                <span>{{ t.role }}</span>
              </div>
            </footer>
          </div>
        </div>

        <div class="funnel__test-dots" role="tablist" aria-label="Navegar testimonios">
          <button
            v-for="(_, i) in testimonials"
            :key="i"
            type="button"
            class="funnel__test-dot"
            :class="{ active: activeTestimonial === i }"
            :aria-label="`Testimonio ${i + 1}`"
            :aria-selected="activeTestimonial === i"
            role="tab"
            @click="activeTestimonial = i"
          />
        </div>
      </div>
    </section>

    <section class="funnel__authority" aria-labelledby="authority-heading">
      <div class="funnel__container funnel__authority-inner">
        <div class="funnel__authority-photo-wrap">
          <div class="funnel__authority-avatar" aria-hidden="true">
            <i class="fa-solid fa-helmet-safety"></i>
          </div>
        </div>
        <div class="funnel__authority-content">
          <p class="funnel__authority-eyebrow">Líder y Fundador</p>
          <h2 id="authority-heading" class="funnel__authority-name">Alejandro Bravo</h2>
          <p class="funnel__authority-role">
            Director Técnico — Recubrimientos Industriales y Arquitectónicos
          </p>
          <p class="funnel__authority-bio">
            En los inicios, aplicábamos como muchos: capas de pintura sobre lo que el cliente traía.
            Hasta que una planta industrial sufrió una parada por deterioro de piso a los 8 meses de
            un trabajo mal ejecutado. Su gerente de operaciones me dijo:
            <strong
              >"No necesito al más barato. Necesito un socio técnico que garantice durabilidad y no
              me cierre la planta cada año."</strong
            >
            Eso cambió todo. Hoy el Método de Recubrimiento Técnico Avanzado es la respuesta a esa
            exigencia.
          </p>
          <ul class="funnel__authority-creds" role="list">
            <li>
              <i class="fa-solid fa-check-circle" aria-hidden="true"></i> Especialista en
              recubrimientos epoxi, poliuretano y poliurea de alto performance
            </li>
            <li>
              <i class="fa-solid fa-check-circle" aria-hidden="true"></i> Certificación en
              preparación de sustratos y aplicación de sistemas industriales
            </li>
            <li>
              <i class="fa-solid fa-check-circle" aria-hidden="true"></i> Gestión integral: del
              diagnóstico técnico al cumplimiento normativo y señalización
            </li>
            <li>
              <i class="fa-solid fa-check-circle" aria-hidden="true"></i> Solo abrimos 5 proyectos
              de gran escala al mes
            </li>
          </ul>
        </div>
      </div>
    </section>

    <section class="funnel__scarcity" aria-labelledby="scarcity-heading">
      <div class="funnel__container">
        <div class="funnel__scarcity-badge">
          <i class="fa-solid fa-gem" aria-hidden="true"></i>
          Atención personalizada de Alejandro Bravo
        </div>
        <h2 id="scarcity-heading" class="funnel__scarcity-title">
          Solo abrimos
          <span class="funnel__scarcity-accent">5 proyectos de gran escala al mes</span>
        </h2>
        <p class="funnel__scarcity-sub">
          Para garantizar el nivel de preparación, dosificación y blindaje regulatorio que nos
          distingue, cada proyecto pasa por la supervisión directa de nuestro equipo técnico. Cuando
          se llenan los 5 cupos, el resto espera al siguiente mes.
        </p>
        <p class="funnel__scarcity-cta-text">
          Agenda hoy tu <strong>Diagnóstico Técnico Avanzado Gratis</strong>
          sin costo — 30 minutos en los que analizaremos tu sustrato, condiciones de uso y riesgos
          normativos para diseñar el recubrimiento correcto desde el primer día.
        </p>
        <button class="funnel__cta-btn funnel__cta-btn--final" @click="openModal()">
          <i class="fa-solid fa-calendar-check" aria-hidden="true"></i>
          AGENDAR MI DIAGNÓSTICO TÉCNICO AVANZADO GRATIS
        </button>
        <p class="funnel__cta-sub">
          <i class="fa-solid fa-lock" aria-hidden="true"></i>
          100% gratuito &nbsp;·&nbsp; Sin compromiso &nbsp;·&nbsp; Cupos limitados
        </p>
      </div>
    </section>

    <footer class="funnel__footer">
      <div class="funnel__container funnel__footer-inner">
        <img :src="dekorpaintLogo" alt="DekorPaint" class="funnel__footer-logo-img" />
        <p class="funnel__footer-desc">Recubrimientos Técnicos Avanzados</p>
        <nav class="funnel__footer-links" aria-label="Legal">
          <RouterLink to="/politicas-privacidad">Política de Privacidad</RouterLink>
          <RouterLink to="/aviso-legal">Aviso Legal</RouterLink>
        </nav>
        <p class="funnel__footer-copy">
          © {{ new Date().getFullYear() }} DekorPaint. Todos los derechos reservados.
        </p>
      </div>
    </footer>
  </div>

  <RegistrationModal :open="modalOpen" @close="modalOpen = false" />
</template>

<style lang="scss" scoped>
@use '@/styles/fonts.modules.scss' as fonts;
@use '@/styles/colorVariables.module.scss' as colors;

.funnel {
  background: #ffffff;
  color: colors.$OS-DARK;
  min-height: 100vh;
  font-family: fonts.$font-secondary;

  &__container {
    max-width: 900px;
    margin: 0 auto;
    padding: 0 1.5rem;
    @media (min-width: 768px) {
      padding: 0 2rem;
    }
  }
}

.funnel__topbar {
  background: #ffffff;
  border-bottom: 1px solid #e8edf5;
  padding: 0.9rem 1.5rem;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.1rem;
  box-shadow: 0 1px 12px rgba(0, 0, 0, 0.06);
}

.funnel__logo-img {
  height: 36px;
  width: auto;
  display: block;
}

.funnel__logo-sub {
  font-size: 0.68rem;
  color: #8a9bb5;
  letter-spacing: 0.06em;
  text-transform: uppercase;
  margin: 0;
}

.funnel__urgency {
  position: sticky;
  top: 0;
  z-index: 50;
  background: linear-gradient(90deg, colors.$QS-URGENT 0%, colors.$QS-URGENT-DARK 100%);
  color: #ffffff;
  padding: 0.65rem 0.85rem;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.6rem;
  flex-wrap: wrap;
  font-family: fonts.$font-interface;
  font-size: 0.82rem;
  font-weight: 700;
  letter-spacing: 0.03em;
  border-bottom: 2px solid colors.$QS-URGENT-LIGHT;
  box-shadow: 0 2px 12px rgba(colors.$QS-URGENT, 0.35);
  text-transform: uppercase;

  @media (min-width: 768px) {
    padding: 0.85rem 1.5rem;
    font-size: 0.95rem;
    justify-content: space-between;
    flex-wrap: nowrap;
  }
}

.funnel__urgency-info {
  display: flex;
  align-items: center;
  gap: 0.6rem;
  flex-wrap: wrap;
  justify-content: center;
  @media (min-width: 768px) {
    flex-wrap: nowrap;
  }
}

.funnel__urgency-cta {
  display: inline-flex;
  align-items: center;
  gap: 0.4rem;
  background: #ffffff;
  color: colors.$QS-URGENT-DARK;
  border: 2px solid colors.$QS-URGENT-LIGHT;
  border-radius: 999px;
  padding: 0.45rem 1rem;
  font-family: fonts.$font-accent;
  font-size: 0.78rem;
  font-weight: 900;
  letter-spacing: 0.05em;
  text-transform: uppercase;
  cursor: pointer;
  white-space: nowrap;
  box-shadow: 0 4px 14px rgba(0, 0, 0, 0.25);
  animation: cta-bounce 2.4s ease-in-out infinite;
  transition:
    transform 0.15s ease,
    box-shadow 0.2s ease,
    background 0.2s ease;

  span {
    font-size: 0.95rem;
    transition: transform 0.18s ease;
  }

  &:hover {
    background: #fff5f5;
    transform: translateY(-1px);
    box-shadow: 0 6px 18px rgba(0, 0, 0, 0.3);
    span {
      transform: translateX(3px);
    }
  }

  &:active {
    transform: translateY(0);
  }

  @media (min-width: 768px) {
    padding: 0.55rem 1.2rem;
    font-size: 0.85rem;
  }
}

@keyframes cta-bounce {
  0%,
  100% {
    box-shadow: 0 4px 14px rgba(0, 0, 0, 0.25);
    transform: scale(1);
  }
  50% {
    box-shadow: 0 6px 20px rgba(0, 0, 0, 0.4);
    transform: scale(1.04);
  }
}

.funnel__proof {
  position: fixed;
  bottom: 1rem;
  left: 1rem;
  z-index: 1100;
  display: flex;
  align-items: flex-start;
  gap: 0.75rem;
  background: #ffffff;
  border: 1px solid rgba(colors.$OS-NAVY, 0.08);
  border-left: 4px solid colors.$OS-BLUE;
  border-radius: 12px;
  padding: 0.75rem 0.9rem 0.75rem 0.85rem;
  box-shadow: 0 12px 36px rgba(0, 0, 0, 0.18);
  max-width: 320px;
  font-family: fonts.$font-secondary;

  @media (min-width: 768px) {
    bottom: 1.5rem;
    left: 1.5rem;
    max-width: 360px;
    padding: 0.9rem 1rem 0.9rem 0.95rem;
  }
}

.funnel__proof-icon {
  flex-shrink: 0;
  width: 34px;
  height: 34px;
  border-radius: 50%;
  background: rgba(colors.$OS-BLUE, 0.14);
  color: colors.$OS-BLUE;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1.05rem;
  margin-top: 2px;
}

.funnel__proof-body {
  flex: 1;
  min-width: 0;
  display: flex;
  flex-direction: column;
  gap: 2px;
}

.funnel__proof-title {
  margin: 0;
  display: flex;
  flex-wrap: wrap;
  align-items: baseline;
  gap: 6px;
  font-family: fonts.$font-interface;
  font-size: 0.86rem;
  line-height: 1.3;
  color: colors.$OS-NAVY;

  strong {
    font-weight: 800;
  }
  span {
    font-size: 0.74rem;
    color: rgba(colors.$OS-NAVY, 0.55);
    font-weight: 500;
  }
}

.funnel__proof-text {
  margin: 0;
  font-size: 0.82rem;
  line-height: 1.35;
  color: rgba(colors.$OS-NAVY, 0.78);
  strong {
    color: colors.$OS-NAVY;
    font-weight: 700;
  }
}

.funnel__proof-meta {
  margin: 4px 0 0;
  font-size: 0.7rem;
  color: rgba(colors.$OS-NAVY, 0.45);
  display: inline-flex;
  align-items: center;
  gap: 4px;
  i {
    font-size: 0.66rem;
  }
}

.funnel__proof-close {
  position: absolute;
  top: 6px;
  right: 6px;
  width: 22px;
  height: 22px;
  border-radius: 50%;
  border: none;
  background: transparent;
  color: rgba(colors.$OS-NAVY, 0.4);
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 0.78rem;
  transition:
    background 0.15s,
    color 0.15s;
  &:hover {
    background: rgba(colors.$OS-NAVY, 0.08);
    color: colors.$OS-NAVY;
  }
}

.proof-fade-enter-active {
  transition:
    opacity 0.32s ease,
    transform 0.42s cubic-bezier(0.34, 1.4, 0.64, 1);
}
.proof-fade-leave-active {
  transition:
    opacity 0.22s ease,
    transform 0.25s ease;
}
.proof-fade-enter-from {
  opacity: 0;
  transform: translateY(14px) translateX(-8px);
}
.proof-fade-leave-to {
  opacity: 0;
  transform: translateY(8px);
}

.funnel__urgency-icon {
  color: colors.$QS-URGENT-LIGHT;
  font-size: 1.1rem;
  filter: drop-shadow(0 0 6px rgba(colors.$QS-URGENT-LIGHT, 0.6));
  animation: bolt-flash 1.8s infinite;
}

@keyframes bolt-flash {
  0%,
  100% {
    opacity: 1;
    transform: scale(1);
  }
  50% {
    opacity: 0.55;
    transform: scale(0.92);
  }
}

.funnel__urgency-text {
  strong {
    font-weight: 900;
    color: #ffe4e4;
    letter-spacing: 0.04em;
  }
}

.funnel__urgency-dot {
  width: 9px;
  height: 9px;
  border-radius: 50%;
  background: colors.$QS-URGENT-LIGHT;
  flex-shrink: 0;
  animation: dot-pulse 1.5s infinite;
  box-shadow: 0 0 0 0 rgba(colors.$QS-URGENT-LIGHT, 0.6);
}

@keyframes dot-pulse {
  0% {
    box-shadow: 0 0 0 0 rgba(255, 107, 107, 0.7);
    transform: scale(1);
  }
  70% {
    box-shadow: 0 0 0 8px rgba(255, 107, 107, 0);
    transform: scale(1.1);
  }
  100% {
    box-shadow: 0 0 0 0 rgba(255, 107, 107, 0);
    transform: scale(1);
  }
}

.funnel__timer {
  display: flex;
  align-items: center;
  gap: 0.2rem;
  background: rgba(0, 0, 0, 0.18);
  padding: 0.25rem 0.6rem;
  border-radius: 8px;
  border: 1px solid rgba(255, 255, 255, 0.18);
}

.funnel__timer-block {
  display: flex;
  align-items: baseline;
  gap: 2px;
  strong {
    font-size: 1.6rem;
    font-weight: 900;
    line-height: 1;
    font-variant-numeric: tabular-nums;
    color: #ffffff;
    @media (min-width: 768px) {
      font-size: 2rem;
    }
  }
  small {
    font-size: 0.72rem;
    opacity: 0.85;
    font-weight: 700;
    text-transform: uppercase;
  }
}

.funnel__timer-sep {
  font-weight: 900;
  font-size: 1.4rem;
  opacity: 0.7;
  padding: 0 2px;
  line-height: 1;
  @media (min-width: 768px) {
    font-size: 1.7rem;
  }
}

.funnel__urgency-callout {
  display: flex;
  align-items: center;
  gap: 0.65rem;
  background: colors.$QS-URGENT-BG;
  border-left: 4px solid colors.$QS-URGENT;
  border-radius: 8px;
  padding: 0.85rem 1rem;
  margin: 1.25rem 0 1.5rem;
  font-family: fonts.$font-interface;
  font-size: 0.92rem;
  color: colors.$OS-DARK;
  font-weight: 600;

  i {
    color: colors.$QS-URGENT;
    font-size: 1.15rem;
    flex-shrink: 0;
  }
  strong {
    color: colors.$QS-URGENT-DARK;
    font-weight: 800;
  }

  @media (min-width: 768px) {
    font-size: 1rem;
    padding: 1rem 1.25rem;
  }
}

.funnel__hero {
  padding: 3.5rem 0 3rem;
  background: linear-gradient(180deg, #eef4ff 0%, #ffffff 70%);
}

.funnel__eyebrow {
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  background: rgba(colors.$OS-NAVY, 0.06);
  border: 1px solid rgba(colors.$OS-NAVY, 0.14);
  border-radius: 999px;
  padding: 0.35rem 0.85rem;
  color: colors.$OS-NAVY;
  font-family: fonts.$font-interface;
  font-size: 0.78rem;
  font-weight: 700;
  letter-spacing: 0.05em;
  text-transform: uppercase;
  margin: 0 0 1.4rem;
  i {
    font-size: 0.8rem;
  }
}

.funnel__headline {
  @include fonts.heading-font(800);
  font-size: clamp(2rem, 5vw, 3.1rem);
  line-height: 1.15;
  color: colors.$OS-DARK;
  margin: 0 0 1rem;
  letter-spacing: -0.025em;

  &-accent {
    color: colors.$OS-RED;
  }
}

.funnel__hero-sub {
  font-size: 1rem;
  color: #4a5f7a;
  line-height: 1.65;
  margin: 0 0 1.5rem;
  max-width: 720px;

  strong {
    color: colors.$OS-DARK;
    font-weight: 700;
  }
}

.funnel__benefits {
  list-style: none;
  padding: 0;
  margin: 0 0 2rem;
  display: flex;
  flex-direction: column;
  gap: 0.55rem;
}

.funnel__benefit {
  display: flex;
  align-items: center;
  gap: 0.6rem;
  font-size: 0.93rem;
  color: #3a4f6a;

  i {
    color: colors.$OS-BLUE;
    font-size: 0.82rem;
    flex-shrink: 0;
  }
}

.funnel__vsl-wrap {
  margin-bottom: 2rem;
}

.funnel__vsl {
  position: relative;
  width: 100%;
  aspect-ratio: 16 / 9;
  border-radius: 16px;
  overflow: hidden;
  cursor: pointer;
  border: 2px solid #d8e6f5;
  box-shadow: 0 8px 40px rgba(0, 82, 165, 0.12);
  transition:
    transform 0.2s ease,
    box-shadow 0.2s ease;

  &:hover {
    transform: translateY(-2px);
    box-shadow: 0 16px 48px rgba(0, 82, 165, 0.2);
  }
  &:focus-visible {
    outline: 3px solid colors.$OS-BLUE;
    outline-offset: 2px;
  }
}

.funnel__vsl-bg {
  position: absolute;
  inset: 0;
  background: colors.$OS-NAVY;
  display: flex;
  align-items: center;
  justify-content: center;
}

.funnel__vsl-thumb {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
  filter: blur(8px) brightness(0.6);
  transform: scale(1.1);
  transition:
    filter 0.4s ease,
    transform 0.4s ease,
    brightness 0.4s ease;

  .funnel__vsl:hover & {
    filter: blur(4px) brightness(0.75);
    transform: scale(1.05);
  }
}

.funnel__vsl-blur-overlay {
  position: absolute;
  inset: 0;
  background: radial-gradient(
    circle at center,
    rgba(colors.$OS-NAVY, 0.2) 0%,
    rgba(colors.$OS-NAVY, 0.6) 100%
  );
  z-index: 1;
}

.funnel__vsl-overlay {
  position: absolute;
  inset: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 1.25rem;
  z-index: 2;
}

.funnel__vsl-play {
  width: 76px;
  height: 76px;
  border-radius: 50%;
  background: #ffffff;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 4px 24px rgba(0, 0, 0, 0.35);
  transition: transform 0.2s ease;
  .funnel__vsl:hover & {
    transform: scale(1.1);
  }
  i {
    color: colors.$OS-RED;
    font-size: 1.7rem;
    margin-left: 5px;
  }
}

.funnel__vsl-caption {
  color: #ffffff;
  font-size: 0.88rem;
  font-weight: 600;
  text-align: center;
  padding: 0 2rem;
  text-shadow: 0 1px 6px rgba(0, 0, 0, 0.5);
  max-width: 420px;
}

.funnel__cta-wrap {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.75rem;
}

.funnel__cta-btn {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 0.6rem;
  background: colors.$OS-RED;
  color: #ffffff;
  border: none;
  border-radius: 12px;
  padding: 1.1rem 2.5rem;
  font-family: fonts.$font-accent;
  font-size: 1rem;
  font-weight: 800;
  letter-spacing: 0.05em;
  cursor: pointer;
  width: 100%;
  max-width: 520px;
  transition:
    background 0.2s ease,
    transform 0.15s ease,
    box-shadow 0.2s ease;
  box-shadow: 0 4px 20px rgba(204, 0, 0, 0.35);

  &:hover {
    background: #aa0000;
    transform: translateY(-1px);
    box-shadow: 0 8px 28px rgba(204, 0, 0, 0.45);
  }
  &:active {
    transform: translateY(0);
  }
}

.funnel__cta-sub {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.4rem;
  font-size: 0.78rem;
  color: #8a9bb5;
  margin: 0;
  i {
    font-size: 0.72rem;
  }
}

.funnel__stats {
  background: colors.$OS-NAVY;
  padding: 3rem 0;
}

.funnel__section-label {
  font-family: fonts.$font-interface;
  font-size: 0.78rem;
  font-weight: 700;
  letter-spacing: 0.08em;
  text-transform: uppercase;
  color: colors.$OS-NAVY;
  margin: 0 0 1rem;

  &--light {
    color: rgba(#ffffff, 0.55);
  }
}

.funnel__stats-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 2rem;
  @media (max-width: 580px) {
    grid-template-columns: 1fr;
    gap: 1.75rem;
  }
}

.funnel__stat {
  text-align: center;
}

.funnel__stat-icon {
  font-size: 1.6rem;
  color: rgba(#ffffff, 0.35);
  margin-bottom: 0.5rem;
}

.funnel__stat-number {
  display: block;
  @include fonts.heading-font(800);
  font-size: 2.6rem;
  color: #ffffff;
  line-height: 1;
  margin-bottom: 0.4rem;
  letter-spacing: -0.03em;
}

.funnel__stat-text {
  font-size: 0.83rem;
  color: rgba(#ffffff, 0.7);
  line-height: 1.45;
  margin: 0;
}

.funnel__problem {
  padding: 4rem 0;
  background: #ffffff;
}

.funnel__section-title {
  @include fonts.heading-font(800);
  font-size: clamp(1.6rem, 3.5vw, 2.2rem);
  color: colors.$OS-DARK;
  margin: 0.25rem 0 1rem;
  line-height: 1.2;
  letter-spacing: -0.02em;
}

.funnel__problem-intro {
  font-size: 0.95rem;
  color: #4a5f7a;
  line-height: 1.6;
  margin: 0 0 1.5rem;
}

.funnel__problem-grid {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.funnel__problem-item {
  display: flex;
  gap: 1rem;
  align-items: flex-start;
  padding: 1.25rem;
  background: #f9fbff;
  border: 1px solid #e4edf7;
  border-radius: 12px;

  strong {
    display: block;
    color: colors.$OS-DARK;
    font-size: 0.93rem;
    font-weight: 700;
    margin-bottom: 0.2rem;
  }
  p {
    font-size: 0.86rem;
    color: #4a5f7a;
    line-height: 1.5;
    margin: 0;
  }
}

.funnel__problem-icon {
  font-size: 1.2rem;
  color: colors.$OS-RED;
  flex-shrink: 0;
  margin-top: 2px;
}

.funnel__method {
  padding: 4rem 0;
  background: #f5f8ff;
}

.funnel__method .funnel__container {
  display: flex;
  flex-direction: column;
  gap: 1.25rem;
}

.funnel__pillar-card {
  display: flex;
  gap: 1.5rem;
  align-items: flex-start;
  background: #ffffff;
  border: 1px solid #e4edf7;
  border-radius: 16px;
  padding: 1.75rem 2rem;
  position: relative;
  box-shadow: 0 2px 12px rgba(0, 82, 165, 0.05);

  @media (max-width: 640px) {
    flex-direction: column;
    padding: 1.5rem;
  }
}

.funnel__pillar-num {
  flex-shrink: 0;
  @include fonts.heading-font(800);
  font-size: 2.8rem;
  color: rgba(colors.$OS-NAVY, 0.07);
  line-height: 1;
  user-select: none;
  width: 48px;
}

.funnel__pillar-content {
  flex: 1;
}

.funnel__pillar-icon {
  width: 44px;
  height: 44px;
  border-radius: 10px;
  background: colors.$OS-NAVY;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 0.85rem;
  i {
    color: #ffffff;
    font-size: 1.1rem;
  }
}

.funnel__pillar-title {
  @include fonts.heading-font(700);
  font-size: 1.05rem;
  color: colors.$OS-DARK;
  margin: 0 0 0.5rem;
}

.funnel__pillar-body {
  font-size: 0.9rem;
  color: #4a5f7a;
  line-height: 1.6;
  margin: 0;
}

.funnel__testimonials {
  padding: 4rem 0;
  background: #ffffff;
}

.funnel__test-carousel {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.funnel__test-card {
  background: #f5f8ff;
  border: 1px solid rgba(colors.$OS-NAVY, 0.1);
  border-left: 4px solid colors.$OS-NAVY;
  border-radius: 16px;
  padding: 2rem;
  max-width: 720px;
  margin: 0 auto;
  box-shadow: 0 4px 24px rgba(0, 82, 165, 0.07);
}

.funnel__test-quote {
  font-size: 2.2rem;
  color: rgba(colors.$OS-NAVY, 0.12);
  display: block;
  margin-bottom: 0.75rem;
  line-height: 1;
}

.funnel__test-text {
  font-size: 1rem;
  color: colors.$OS-DARK;
  line-height: 1.65;
  margin: 0 0 1.5rem;
  font-style: italic;
}

.funnel__test-author {
  display: flex;
  align-items: center;
  gap: 0.75rem;

  strong {
    display: block;
    color: colors.$OS-DARK;
    font-size: 0.9rem;
    font-weight: 700;
  }
  span {
    font-size: 0.78rem;
    color: #8a9bb5;
  }
}

.funnel__test-avatar {
  width: 44px;
  height: 44px;
  border-radius: 50%;
  background: colors.$OS-NAVY;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  i {
    color: #ffffff;
    font-size: 1.2rem;
  }
}

.funnel__test-dots {
  display: flex;
  justify-content: center;
  gap: 0.6rem;
  margin-top: 1.25rem;
}

.funnel__test-dot {
  width: 10px;
  height: 10px;
  border-radius: 50%;
  border: none;
  background: #d0dbe8;
  cursor: pointer;
  transition:
    background 0.2s,
    transform 0.2s;
  padding: 0;

  &.active {
    background: colors.$OS-NAVY;
    transform: scale(1.3);
  }
  &:hover {
    background: colors.$OS-BLUE;
  }
}

.funnel__authority {
  padding: 4rem 0;
  background: linear-gradient(135deg, #eef4ff 0%, #f9fbff 100%);
  border-top: 1px solid #e4edf7;
  border-bottom: 1px solid #e4edf7;
}

.funnel__authority-inner {
  display: flex;
  gap: 2.5rem;
  align-items: flex-start;
  @media (max-width: 640px) {
    flex-direction: column;
    align-items: center;
  }
}

.funnel__authority-photo-wrap {
  flex-shrink: 0;
}

.funnel__authority-avatar {
  width: 112px;
  height: 112px;
  border-radius: 50%;
  background: colors.$OS-NAVY;
  display: flex;
  align-items: center;
  justify-content: center;
  border: 4px solid #ffffff;
  box-shadow: 0 4px 20px rgba(0, 82, 165, 0.2);
  i {
    color: rgba(#ffffff, 0.7);
    font-size: 3rem;
  }
}

.funnel__authority-content {
  flex: 1;
}

.funnel__authority-eyebrow {
  font-family: fonts.$font-interface;
  font-size: 0.76rem;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 0.08em;
  color: colors.$OS-BLUE;
  margin: 0 0 0.35rem;
}

.funnel__authority-name {
  @include fonts.heading-font(800);
  font-size: 2rem;
  color: colors.$OS-DARK;
  margin: 0 0 0.2rem;
  letter-spacing: -0.02em;
}

.funnel__authority-role {
  font-size: 0.88rem;
  color: #8a9bb5;
  margin: 0 0 1rem;
}

.funnel__authority-bio {
  font-size: 0.93rem;
  color: #3a4f6a;
  line-height: 1.65;
  margin: 0 0 1rem;
  strong {
    color: colors.$OS-DARK;
    font-weight: 700;
  }
}

.funnel__authority-creds {
  list-style: none;
  padding: 0;
  margin: 0;
  display: flex;
  flex-direction: column;
  gap: 0.4rem;

  li {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    font-size: 0.86rem;
    color: #3a4f6a;
    i {
      color: colors.$OS-BLUE;
      font-size: 0.82rem;
      flex-shrink: 0;
    }
  }
}

.funnel__scarcity {
  padding: 4.5rem 0;
  background: colors.$OS-NAVY;
  text-align: center;
}

.funnel__scarcity-badge {
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.35rem 1rem;
  border-radius: 999px;
  background: rgba(#ffffff, 0.08);
  border: 1px solid rgba(#ffffff, 0.18);
  font-family: fonts.$font-interface;
  font-size: 0.75rem;
  font-weight: 700;
  letter-spacing: 0.06em;
  text-transform: uppercase;
  color: rgba(#ffffff, 0.75);
  margin-bottom: 1rem;
  i {
    color: rgba(#ffffff, 0.5);
    font-size: 0.8rem;
  }
}

.funnel__scarcity-title {
  @include fonts.heading-font(800);
  font-size: clamp(1.8rem, 4vw, 2.8rem);
  color: #ffffff;
  margin: 0 0 1rem;
  letter-spacing: -0.025em;
  line-height: 1.15;
}

.funnel__scarcity-accent {
  color: colors.$OS-RED;
}

.funnel__scarcity-sub {
  font-size: 1rem;
  color: rgba(#ffffff, 0.65);
  margin: 0 auto 1.5rem;
  max-width: 560px;
  line-height: 1.6;
}

.funnel__scarcity-cta-text {
  font-size: 1.05rem;
  color: rgba(#ffffff, 0.85);
  margin: 0 auto 2rem;
  max-width: 560px;
  line-height: 1.55;

  strong {
    color: #ffffff;
    font-weight: 700;
  }
}

.funnel__cta-btn--final {
  margin: 0 auto 1rem;
  background: colors.$OS-RED;
  box-shadow: 0 4px 24px rgba(204, 0, 0, 0.4);
  max-width: 560px;
  &:hover {
    background: #aa0000;
  }
}

.funnel__scarcity .funnel__cta-sub {
  color: rgba(#ffffff, 0.5);
}

.funnel__footer {
  background: colors.$OS-DARK;
  padding: 2rem 1.5rem;
}

.funnel__footer-inner {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.75rem;
  text-align: center;
}

.funnel__footer-logo-img {
  height: 32px;
  width: auto;
  display: block;
  opacity: 0.7;
}

.funnel__footer-desc {
  font-size: 0.72rem;
  color: rgba(#ffffff, 0.35);
  text-transform: uppercase;
  letter-spacing: 0.06em;
  margin: 0;
}

.funnel__footer-links {
  display: flex;
  gap: 1.5rem;
  a {
    font-size: 0.78rem;
    color: rgba(#ffffff, 0.45);
    text-decoration: none;
    transition: color 0.2s;
    &:hover {
      color: rgba(#ffffff, 0.85);
    }
  }
}

.funnel__footer-copy {
  font-size: 0.72rem;
  color: rgba(#ffffff, 0.28);
  margin: 0;
}
</style>
