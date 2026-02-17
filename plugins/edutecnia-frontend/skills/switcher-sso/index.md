---
name: switcher-sso
description: Switcher cross-app navigation and SSO cookie authentication pattern for Edutecnia ecosystem. Covers the standalone app, embeddable widget, cookie-based auth, and cross-app logout detection.
---

# Switcher y SSO Cross-App

> **Alcance:** Solo aplica a proyectos del ecosistema Edutecnia. No usar en otros proyectos.

## Descripcion
El Switcher es el sistema de navegacion entre aplicaciones de Edutecnia. Tiene dos componentes: una app Nuxt 4 standalone (`edutecnia-switcher/`, puerto 8047) que muestra un grid de apps, y un widget vanilla JS embebible (`widget.js`) que se inyecta en otras apps. Ambos dependen del SSO via cookie para funcionar sin login propio.

## Arquitectura General

```
┌──────────────────────────────────────────────────────────────┐
│                    SSO Cookie Flow                            │
│                                                              │
│  Login (Portal :8040)                                        │
│    POST /api/v1/portal/auth/login                            │
│    Backend setea cookie: edutecnia_token (JWT, 300 dias)     │
│    Domain: .edutecnia.cl (prod) | host-only (localhost)      │
│                                                              │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐    │
│  │ Portal   │  │ Switcher │  │  Docs    │  │ Config   │    │
│  │  :8040   │  │  :8047   │  │  :8042   │  │  :8043   │    │
│  └────┬─────┘  └────┬─────┘  └────┬─────┘  └────┬─────┘    │
│       │              │              │              │          │
│       └──────────────┴──────────────┴──────────────┘          │
│                          │                                    │
│            fetch(/profile, {credentials:'include'})           │
│            Browser envia cookie automaticamente               │
│                          │                                    │
│                   ┌──────▼──────┐                             │
│                   │ Backend API │                             │
│                   │   :3040     │                             │
│                   │ Authenticate│                             │
│                   │   concern   │                             │
│                   └─────────────┘                             │
│                   1. Authorization header (Bearer)            │
│                   2. Fallback: cookies['edutecnia_token']     │
└──────────────────────────────────────────────────────────────┘
```

## Componentes del Switcher

### 1. App Standalone (edutecnia-switcher/)
Grid full-page con todas las apps accesibles. No tiene login propio.

| Archivo | Proposito |
|---------|-----------|
| `pages/index.vue` | Grid de apps con filtro por permisos |
| `pages/logout.vue` | Cierra sesion y redirige al portal |
| `stores/auth.ts` | Auth store SSO (fetchProfile, initAuth, logout) |
| `middleware/auth.global.ts` | Redirige a portal si no hay sesion |
| `plugins/sso-logout-watch.client.ts` | Detecta logout global via visibilitychange |
| `public/widget.js` | Widget embebible (no usa framework) |
| `nuxt.config.ts` | apiBase, portalUrl en runtimeConfig |

### 2. Widget Embebible (public/widget.js)
Script vanilla JS que se carga via `<script>` en cualquier app.

```
Dos modos de renderizado:
1. Embedded: Si existe <div id="edutecnia-switcher">, renderiza inline
2. Floating: Si no existe container, renderiza como boton fijo top-right
```

### 3. Integracion en Portal (edutecnia-front/)
El portal carga el widget e inyecta categorias de permisos.

| Archivo | Proposito |
|---------|-----------|
| `src/components/Breadcrumb.vue` | Container #edutecnia-switcher + carga widget |
| `src/store/modules/administration/permissions.ts` | Getter accessibleCategories |
| `src/router/index.ts` | trySsoAuth() + visibilitychange logout |
| `.env` | VUE_APP_SWITCHER_URL |

## SSO: Cookie Backend

### Seteo de Cookie (auth_controller.rb)
```ruby
def set_sso_cookie(jwt)
  cookie_options = {
    value: jwt,
    httponly: false,       # Accesible via JS (useCookie)
    secure: Rails.env.production?,
    same_site: :lax,
    expires: 300.days.from_now,
    path: '/'
  }
  # En produccion: domain .edutecnia.cl (compartido entre subdominios)
  if ENV['SSO_COOKIE_DOMAIN'].present?
    cookie_options[:domain] = ENV['SSO_COOKIE_DOMAIN']
  end
  response.set_cookie('edutecnia_token', cookie_options)
end
```

### Dual Auth (authenticate.rb)
```ruby
def current_user
  token = if authorization_header.present?
            authorization_clean              # 1. Bearer token (header)
          elsif request.cookies['edutecnia_token'].present?
            request.cookies['edutecnia_token'] # 2. SSO cookie (fallback)
          end
  # Decodifica JWT y busca User
end
```

### Variables de Entorno
| Variable | Donde | Valor Dev | Valor Prod |
|----------|-------|-----------|------------|
| `SSO_COOKIE_DOMAIN` | Backend Rails | (no se setea, host-only) | `.edutecnia.cl` |
| `VUE_APP_SWITCHER_URL` | Portal .env | `http://localhost:8047/widget.js` | `https://switcher.edutecnia.cl/widget.js` |
| `NUXT_PUBLIC_API_BASE` | Switcher | `http://localhost:3040` | `https://back.edutecnia.cl` |
| `NUXT_PUBLIC_PORTAL_URL` | Switcher | `http://localhost:8040` | `https://portal.edutecnia.cl` |

## Filtrado de Apps por Permisos

### Flujo
```
Permissions Store (portal)
  accessibleCategories getter
    → extrae categorias con read=true y blockedByPlan=false
    → retorna: ['pedagogia', 'planificacion', 'rrhh', 'administracion']

Breadcrumb.vue (portal)
  watch accessibleCategories
    → container.setAttribute('data-categories', categories.join(','))

widget.js
  getVisibleApps()
    → lee data-categories del container
    → filtra APPS por match de categorias
    → apps con categories=null siempre visibles (ej: Ayuda)
```

### Mapeo Categoria → App
| Categoria | App | Subdomain | Puerto Local |
|-----------|-----|-----------|-------------|
| `pedagogia`, `planificacion`, `pie` | Portal | portal | 8040 |
| `rrhh` | RRHH | rrhh | 8044 |
| `contabilidad` | Contabilidad | accounting | 8045 |
| `administracion` | Configuracion | config | 8043 |
| `sed` | Evaluacion Docente | sed | 8046 |
| (siempre visible) | Ayuda | docs | 8042 |

### Categorias del Backend (Permission::CATEGORIES)
```ruby
CATEGORIES = {
  'pedagogia'      => { label: 'Gestion Pedagogica', icon: 'book-open' },
  'planificacion'  => { label: 'Planificacion',      icon: 'calendar' },
  'pie'            => { label: 'PIE',                 icon: 'heart' },
  'rrhh'           => { label: 'Recursos Humanos',    icon: 'briefcase' },
  'contabilidad'   => { label: 'Contabilidad',        icon: 'dollar-sign' },
  'sed'            => { label: 'Evaluacion Docente',  icon: 'check-circle' },
  'administracion' => { label: 'Administracion',      icon: 'settings' }
}
```

## Deteccion de Logout Global

Todas las apps (portal, switcher, docs) implementan el mismo patron:

```javascript
// visibilitychange: cuando el usuario vuelve a la pestana
document.addEventListener('visibilitychange', async () => {
  if (document.visibilityState !== 'visible') return
  if (!isAuthenticated()) return

  // Throttle: max 1 vez cada 30 segundos
  const now = Date.now()
  if (now - lastCheck < 30000) return
  lastCheck = now

  const response = await fetch('/api/v1/portal/auth/profile', {
    credentials: 'include'
  })
  if (!response.ok) {
    // Cookie invalida → sesion cerrada desde otra app → logout local
  }
})
```

## Flujo Completo: Login → Switcher → Navegacion

```
1. Usuario abre portal.edutecnia.cl/login
2. POST /login → backend genera JWT + setea cookie edutecnia_token
3. Portal carga → permissions store → accessibleCategories
4. Breadcrumb.vue carga widget.js + setea data-categories
5. Widget renderiza boton en la barra superior

6. Usuario hace click en boton del widget
7. widget.js lee data-categories y filtra apps visibles
8. Dropdown muestra solo apps permitidas por plan

9. Usuario navega a rrhh.edutecnia.cl
10. RRHH middleware: fetch(/profile, credentials:include)
11. Browser envia cookie edutecnia_token automaticamente
12. Backend valida JWT del cookie → sesion OK → contenido

13. Si el usuario cierra sesion en cualquier app:
    - Backend limpia cookie (clear_sso_cookie)
    - Las demas apps detectan logout via visibilitychange
    - Auto-redirect a portal/login
```

## Integracion en Apps Nuxt 4

### Cargar widget (app.vue)
```ts
function loadSwitcherWidget() {
  if ((window as unknown as Record<string, unknown>).__edutecniaSwitcher) return
  const h = window.location.hostname
  const isLocal = h === 'localhost' || h === '127.0.0.1'
  const src = isLocal
    ? 'http://localhost:8047/widget.js'
    : (h.includes('staging') ? 'https://switcher.staging.edutecnia.cl/widget.js' : 'https://switcher.edutecnia.cl/widget.js')
  const script = document.createElement('script')
  script.src = src
  script.async = true
  document.head.appendChild(script)
}
```

### Container en AppBar
```vue
<!-- En UDashboardNavbar #right slot -->
<div id="edutecnia-switcher" />
```

### Plugin SSO Logout Watch
```ts
// plugins/sso-logout-watch.client.ts
export default defineNuxtPlugin(() => {
  const authStore = useAuthStore()
  const config = useRuntimeConfig()

  let lastCheck = 0
  document.addEventListener('visibilitychange', async () => {
    if (document.visibilityState !== 'visible') return
    if (!authStore.isAuthenticated) return
    const now = Date.now()
    if (now - lastCheck < 30000) return
    lastCheck = now
    try {
      const response = await fetch(
        `${config.public.apiBase}/api/v1/portal/auth/profile`,
        { credentials: 'include' },
      )
      if (!response.ok) {
        await authStore.logout()
        const portalUrl = config.public.portalUrl || 'https://portal.edutecnia.cl'
        window.location.href = portalUrl
      }
    } catch { /* Network error — don't logout */ }
  })
})
```

## Consideraciones Tecnicas

### Localhost vs Produccion
- **Localhost:** Cookie es host-only (sin domain), compartida entre puertos del mismo host
- **Produccion:** Cookie con `domain: .edutecnia.cl` compartida entre subdominios
- **Cross-origin:** `document.cookie` NO puede leer cookies seteadas por otro origen. Usar `fetch({credentials:'include'})` para que el browser envie la cookie automaticamente

### No tiene Login Propio
El switcher redirige a `portalUrl` si no detecta sesion. El login es responsabilidad exclusiva del portal.

### Widget Vanilla JS
`widget.js` no usa Vue, Nuxt ni ningun framework. Es IIFE puro con DOM manipulation. Esto permite cargarlo en cualquier app (Vue 2, Nuxt 4, Ionic, etc.) sin conflictos de dependencias.

## Archivos Clave (referencia rapida)

### Backend
- `app/controllers/api/v1/portal/auth_controller.rb` — set_sso_cookie, clear_sso_cookie
- `app/services/authenticate.rb` — dual auth (header + cookie)
- `app/models/permission.rb` — CATEGORIES hash

### Switcher (edutecnia-switcher/)
- `public/widget.js` — widget embebible
- `pages/index.vue` — grid full-page
- `stores/auth.ts` — auth SSO
- `middleware/auth.global.ts` — redirect si no hay sesion
- `plugins/sso-logout-watch.client.ts` — logout global

### Portal (edutecnia-front/)
- `src/components/Breadcrumb.vue` — container + carga widget
- `src/store/modules/administration/permissions.ts` — accessibleCategories
- `src/router/index.ts` — trySsoAuth + visibilitychange
