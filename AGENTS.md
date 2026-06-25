# DekorPaint — VSL Funnel

**Brand**: DekorPaint (recubrimientos técnicos avanzados para industria y residencias exclusivas).  
**Vocero**: Alejandro Bravo.

## Stack

- Vue 3 + Vite 7 + TypeScript + SCSS + Pinia + vue-router
- `@` → `./src` (alias de Vite y de `tsconfig.app.json`)
- pnpm
- FontAwesome 6 vía CDN en `index.html`: usar `<i class="fa-solid fa-...">`, **nunca emojis**
- SCSS: `colorVariables.module.scss` auto-inyectado global vía `additionalData` en `vite.config.ts`
- Wistia player (`wistia-player` custom element, media-id: `e3o2zxds45`)
- GSAP + ScrollTrigger en `ToolsView.vue` (existe en disco pero **no está ruteada**)

## Comandos

```sh
pnpm dev           # dev server
pnpm build         # run-p type-check "build-only" — en paralelo vía npm-run-all2
pnpm build-only    # vite build (sin type-check)
pnpm type-check    # vue-tsc --build (usa project references de tsconfig)
pnpm format        # prettier --write src/
pnpm preview       # vite preview
```

Prettier (`.prettierrc.json`): sin semicolons, single quotes, `printWidth: 100`.

## Rutas y flujo del funnel

```
/                      → FunnelView   (landing + RegistrationModal)
/registro-vsl-tr       → alias de /
/ver-video             → VideoView    (VSL + timer 2 min + overlay de captura)
/agendar               → BookingView  (iframe de calendario GHL)
/cita-confirmada       → BookedView
/sin-espacio           → NoSpaceView  (cooldown 48 h)
/politicas-privacidad  → PrivacyPolicyView  (pública, sin guard)
/aviso-legal           → LegalNoticeView    (pública, sin guard)
```

## Routing guards

Centralizados en `src/router/index.ts` (~línea 177).

- **`os_booked_at`**: TTL 3 días. Si está fresco → cualquier ruta redirige a `/cita-confirmada`.
- **`os_disq_at`**: TTL 48 h. Si está fresco → `/agendar` y `/cita-confirmada` redirigen a `/sin-espacio`.
- `/sin-espacio` sin `os_disq_at` fresco → redirige a `/`.
- `/cita-confirmada` sin `os_booked_at` fresco → redirige a `/`.
- Las rutas legales saltan los guards.

## LocalStorage / sessionStorage

| Key | Value | Escrito por |
|---|---|---|
| `os_contact` | `{ nombre, apellido, negocio, email, telefono, timestamp }` | `RegistrationModal` y `VideoView` capture |
| `os_disq_at` | timestamp ms | `CalendarModal` al descalificar (solo en producción) |
| `os_booked_at` | timestamp ms | `BookingView` al recibir `msgsndr-booking-complete` |
| `os_fb` | `{ fbclid, fbc, fbp, utm_* }` | `FunnelView` en mount (sessionStorage) |
| `alu_page_entry` | timestamp ms | `FunnelView` en mount (sessionStorage) |
| `os_complete_fired` | `'1'` | `BookedView` en mount (sessionStorage) |

## Calificación (`CalendarModal`)

- Descalifica si `presupuesto === 'menos3000'` (< $3,000 USD en el proyecto de recubrimiento).
- Al descalificar: guarda `os_disq_at` y redirige a `/sin-espacio`. El guardado se omite en `localhost`.
- Al calificar: dispara pixel `Lead` y redirige a `/agendar`.

## GHL / tracking

- Calendar URL: `https://api.leadconnectorhq.com/widget/booking/AV9mxK0SEB9vcYGDEKjP`
- Prefill: `?firstName=&email=&phone=` leídos de `os_contact`.
- Confirmación de cita: escucha `postMessage(['msgsndr-booking-complete', ...])`.
- Altura dinámica del iframe: escucha `postMessage({ type: 'booking-app', height: N })`.
- Script embed de GHL: `https://api.leadconnectorhq.com/js/form_embed.js` inyectado en `BookingView`.

### Variables de entorno de webhooks

```sh
VITE_WEBHOOK_REGISTRO       # webhook de registro (RegistrationModal) → URL real de DekorPaint
VITE_WEBHOOK_CALIFICACION   # webhook de cualificación (CalendarModal) → mock/placeholder por ahora
VITE_WEBHOOK_TRACKING       # tracking genérico (ghl.ts) → mock/placeholder por ahora
```

Webhook de registro por defecto en `RegistrationModal.vue`:  
`https://services.leadconnectorhq.com/hooks/BTN0QFH5yTHaUtgYIE1e/webhook-trigger/kQSFLgQk5q9S7rdbJQCH`

## Meta Pixel

- ID actual: `2330357677497487`.
- Eventos trackeados: `PageView`, `ViewContent`, `CompleteRegistration`, `Lead`.
- Parámetros de atribución se capturan en `sessionStorage['os_fb']` vía `src/utils/fbclid.ts`.

## Convenciones de estilo / UX

- No hay `TheHeader`/`TheFooter` globales montados; existen en disco pero `App.vue` solo renderiza `<RouterView />`.
- Usar solo íconos FontAwesome; nunca emojis en la UI.
- `BookedView`: padding centralizado en `.booked__main`.
- Countdown de urgencia en Funnel: se reinicia cada bloque de 6 h.
- Toast de social proof: 3 s de delay inicial, 5 s visibles, 2 s de gap.

## Gotchas de dev / deploy

- `node`: `^20.19.0 || >=22.12.0`.
- Sin tests, sin CI config.
- Dev server permite host: `38828430451a.ngrok-free.app` (en `vite.config.ts`).
- SEO dinámico vía `router.afterEach` (title, description, OG, canonical).
- `ToolsView.vue` no está cableada al router; es una página huérfana en disco.
