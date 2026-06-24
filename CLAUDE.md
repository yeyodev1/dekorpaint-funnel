# DekorPaint — VSL Funnel

## Proyecto
Landing page / funnel de **DekorPaint** (dekorpaint.ec).  
Recubrimientos técnicos avanzados para industria y residencias exclusivas. Líder: Alejandro Bravo.

## Stack
- **Vue 3** + Vite 7 + TypeScript
- **SCSS** con variables en `src/styles/colorVariables.module.scss`
- **GSAP** instalado (en uso en `ToolsView.vue`, no ruteada)
- **pnpm** como package manager
- **vue-router** (rutas del funnel + legales)
- **FontAwesome 6** (CDN en `index.html`) — usar `<i class="fa-solid fa-...">`, NO emojis

## Flujo del Funnel (multi-paso)
```
/ (FunnelView)
  ↓ [form submit → router.push('/ver-video')]
/ver-video (VideoView)            ← VSL Wistia e3o2zxds45; CTA bloqueado 2 min; guard de contacto
  ↓ [popup CalendarModal → cualifica]
/agendar (BookingView)            ← GHL calendar iframe AV9mxK0SEB9vcYGDEKjP (pre-llenado)
  ↓ [msgsndr-booking-complete]
/cita-confirmada (BookedView)     ← Confirmación final con nombre personalizado
  ↓ [no cualifica en CalendarModal]
/sin-espacio (NoSpaceView)        ← Rechazo empático + cooldown 48h
```

## LocalStorage — claves en uso
| Clave | Contenido | Quién lo escribe |
|---|---|---|
| `os_contact` | `{ nombre, apellido, negocio, email, telefono, timestamp }` | RegistrationModal + VideoView capture |
| `os_disq_at` | timestamp (ms) | CalendarModal al disqualificar (presupuesto < $3K) |
| `os_booked_at` | timestamp (ms) | BookingView al confirmar cita |

## Guards de seguridad
- **Router global** (`src/router/index.ts`): TTL 3 días para booking, 48h para disqualificación.
- **FunnelView**: si `os_disq_at` < 48h → redirige a `/sin-espacio` (desactivado en `localhost`).
- **CalendarModal**: `presupuesto < $3,000 USD` → `/sin-espacio` + guarda `os_disq_at`.

## GHL Calendar
- URL: `https://api.leadconnectorhq.com/widget/booking/AV9mxK0SEB9vcYGDEKjP`
- Pre-fill params: `?firstName=&email=&phone=` (leídos de `os_contact.telefono`).
- Evento de confirmación: `postMessage(['msgsndr-booking-complete', {...}])`
- Altura dinámica: `postMessage({ type: 'booking-app', height: N })`

## Webhooks
- **Registro**: `https://services.leadconnectorhq.com/hooks/BTN0QFH5yTHaUtgYIE1e/webhook-trigger/kQSFLgQk5q9S7rdbJQCH`
- **Cualificación**: mock/placeholder (`VITE_WEBHOOK_CALIFICACION` sin fallback).
- **Tracking**: mock/placeholder (`VITE_WEBHOOK_TRACKING` sin fallback).

## Estructura clave
```
src/
  views/
    FunnelView.vue          ← / — landing principal
    VideoView.vue           ← /ver-video — VSL + timer 2 min + contact guard
    BookingView.vue         ← /agendar — GHL calendar iframe
    BookedView.vue          ← /cita-confirmada
    NoSpaceView.vue         ← /sin-espacio
    PrivacyPolicyView.vue   ← /politicas-privacidad (texto legal heredado de Aluvicopp)
    LegalNoticeView.vue     ← /aviso-legal (texto legal heredado de Aluvicopp)
  components/
    RegistrationModal.vue   ← Modal de captura inicial
    CalendarModal.vue       ← Modal de calificación 4 preguntas → routing
  utils/
    ghl.ts                  ← Tracking webhook
    fbclid.ts               ← Atribución Meta Ads
```

## Videos
- **Wistia media-id `e3o2zxds45`** — VSL DekorPaint.

## Funnel — Contenido DekorPaint
- **Headline**: "Blindaje técnico estructurado para industria y residencias exclusivas".
- **Especialista**: Alejandro Bravo.
- **Marca**: DekorPaint.
- **Metodología**: Método de Recubrimiento Técnico Avanzado (3 pilares).
- **Segmentos**: Plantas industriales, bodegas, laboratorios/clínicas, residencias exclusivas, comercios premium.
- **CTA principal**: "AGENDAR DIAGNÓSTICO TÉCNICO AVANZADO GRATIS".

## Colores de marca (DekorPaint)
```scss
$DK-NAVY:    #0B1628   // Deep navy — brand principal
$DK-BLUE:    #0D6EFD   // Technical blue — secundario
$DK-ORANGE:  #F97316   // Energy orange — acento
$DK-RED:     #DC2626   // CTAs y urgencia
```

## Comandos
```bash
pnpm dev        # desarrollo local
pnpm build      # build de producción (type-check primero)
pnpm type-check # TypeScript check
pnpm format     # prettier --write src/
```

## No hacer
- No agregar Header/Footer de navegación al funnel (la app no los monta).
- No usar emojis — usar íconos FontAwesome (`<i class="fa-solid fa-...">`).
- Los componentes en `src/components/booked/` y las páginas legales aún contienen branding antiguo (Aluvicopp); se deben actualizar solo cuando haya texto legal/texto de componente definitivo.
