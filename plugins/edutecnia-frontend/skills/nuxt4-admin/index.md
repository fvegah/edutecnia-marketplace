---
name: nuxt4-admin
description: Standard patterns for Edutecnia Nuxt 4 admin apps (RRHH, Config, Accounting, SED, etc). Auth with SSO cookies + Google OAuth, Nuxt UI components, Tailwind CSS v4, composable patterns.
---

# Edutecnia Nuxt 4 Admin Standard

> **Alcance:** Solo aplica a proyectos del ecosistema Edutecnia. No usar en otros proyectos.

Standard patterns for all Edutecnia Nuxt 4 admin applications. Use when building new apps or features for the platform.

## Technology Stack

- **Framework**: Nuxt 4 (Vue 3 + TypeScript)
- **UI Library**: Nuxt UI v4 (@nuxt/ui ^4.4.0)
- **Styling**: Tailwind CSS v4 with `:root` for custom colors
- **State**: Pinia stores + composables
- **Icons**: Lucide via @nuxt/icon
- **Dates**: dayjs-nuxt (es locale, America/Santiago timezone)

## Project Structure

```
app-name/
├── assets/css/main.css          # Tailwind + Nuxt UI + edutecnia colors
├── app.config.ts                # UI colors (primary: 'edutecnia')
├── nuxt.config.ts               # Runtime config, modules, Firebase
├── composables/
│   ├── useApi.ts                # Base API client ($fetch wrapper)
│   ├── useFirebase.ts           # Google OAuth via Firebase popup
│   ├── useNotification.ts       # Toast notifications
│   └── use{Feature}.ts          # Feature-specific composables
├── stores/
│   └── auth.ts                  # SSO cookie auth + Firebase login
├── layouts/
│   ├── default.vue              # Sidebar + AppBar layout
│   └── auth.vue                 # Centered card layout for login
├── pages/
│   ├── login.vue                # RUT + password + Google OAuth
│   ├── select-establishment.vue # Multi-establishment picker
│   └── {feature}/index.vue      # Feature pages
├── components/
│   └── {feature}/               # Feature-specific components
└── types/                       # TypeScript interfaces
```

## Global Component Sizing

**CRITICAL**: Set default size to `xl` for all Nuxt UI components in `nuxt.config.ts`. The default `md` is too small and cramped for admin apps.

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  modules: ['@nuxt/ui'],
  ui: {
    theme: {
      defaultVariants: {
        size: 'xl',
      },
    },
  },
})
```

This sets `size="xl"` (~44px height) globally for UInput, USelect, UButton, UTextarea, etc. Individual components can still override with explicit `size` props (e.g., icon buttons with `size="xs"`).

## Custom Colors (Tailwind CSS v4)

**CRITICAL**: Use `:root` (not `@theme`) to prevent tree-shaking of CSS variables. Nuxt UI references colors via `var(--color-edutecnia-500)` which requires the variables to exist at runtime.

```css
/* assets/css/main.css */
@import "tailwindcss";
@import "@nuxt/ui";

/* IMPORTANTE: :root y NO @theme — evita tree-shaking */
:root {
  --color-edutecnia-50: #fef6ee;
  --color-edutecnia-100: #fdecd7;
  --color-edutecnia-200: #fad5ae;
  --color-edutecnia-300: #f7b87a;
  --color-edutecnia-400: #f49044;
  --color-edutecnia-500: #ee7525;
  --color-edutecnia-600: #df5915;
  --color-edutecnia-700: #b94213;
  --color-edutecnia-800: #933618;
  --color-edutecnia-900: #762e16;
  --color-edutecnia-950: #401609;
}
```

```ts
// app.config.ts — colors + full-width form components
export default defineAppConfig({
  ui: {
    colors: {
      primary: 'edutecnia',
      neutral: 'slate',
    },
    input: {
      slots: {
        root: 'w-full',
      },
    },
    select: {
      slots: {
        root: 'w-full',
        base: 'w-full',
      },
    },
    selectMenu: {
      slots: {
        root: 'w-full',
        base: 'w-full',
      },
    },
    inputMenu: {
      slots: {
        root: 'w-full',
        base: 'w-full',
      },
    },
    inputNumber: {
      slots: {
        root: 'w-full',
      },
    },
    textarea: {
      slots: {
        root: 'w-full',
      },
    },
  },
})
```

## Authentication: SSO Cookie + Google OAuth

### Runtime Config

```ts
// nuxt.config.ts runtimeConfig.public
{
  apiBase: process.env.NUXT_PUBLIC_API_BASE || 'http://localhost:3040',
  firebaseApiKey: process.env.NUXT_PUBLIC_FIREBASE_API_KEY || '',
  firebaseAuthDomain: 'edutecnia-front.firebaseapp.com',
  firebaseProjectId: 'edutecnia-front',
}
```

### Auth Store (SSO Cookie Pattern)

All portal-level apps (RRHH, Config, Accounting, SED) use the same SSO cookie auth:

```ts
// stores/auth.ts
export const useAuthStore = defineStore('auth', () => {
  const config = useRuntimeConfig()
  const tokenCookie = useCookie<string | null>('edutecnia_token', { sameSite: 'lax' })
  const user = ref<Record<string, unknown> | null>(null)
  const loading = ref(false)
  const isAuthenticated = computed(() => !!user.value)

  // RUT + password login
  async function login(rut: string, password: string) {
    const data = await $fetch<{ user: Record<string, unknown> }>(
      `${config.public.apiBase}/api/v1/portal/auth/login`,
      { method: 'POST', credentials: 'include', body: { rut, password } },
    )
    user.value = data.user
  }

  // Google OAuth login (Firebase ID token exchange)
  async function loginWithFirebase(idToken: string) {
    const data = await $fetch<{ user: Record<string, unknown> }>(
      `${config.public.apiBase}/api/v1/portal/auth/firebase_login`,
      { method: 'POST', credentials: 'include', body: { id_token: idToken } },
    )
    user.value = data.user
  }

  // Profile fetch
  async function fetchProfile() {
    const data = await $fetch<Record<string, unknown>>(
      `${config.public.apiBase}/api/v1/portal/auth/profile`,
      { credentials: 'include' },
    )
    user.value = data
  }

  // Logout
  async function logout() {
    try {
      await $fetch(`${config.public.apiBase}/api/v1/portal/auth/logout`, {
        method: 'DELETE', credentials: 'include',
      })
    } finally {
      user.value = null
      tokenCookie.value = null
    }
  }

  return { user, loading, isAuthenticated, login, loginWithFirebase, fetchProfile, logout }
})
```

### Firebase Composable (Google OAuth)

```ts
// composables/useFirebase.ts
import { initializeApp, type FirebaseApp } from 'firebase/app'
import { getAuth, signInWithPopup, GoogleAuthProvider, type Auth } from 'firebase/auth'

let app: FirebaseApp | null = null
let auth: Auth | null = null

function initFirebase(): Auth {
  if (auth) return auth
  const config = useRuntimeConfig()
  app = initializeApp({
    apiKey: config.public.firebaseApiKey as string,
    authDomain: config.public.firebaseAuthDomain as string,
    projectId: config.public.firebaseProjectId as string,
  })
  auth = getAuth(app)
  return auth
}

export function useFirebase() {
  async function signInWithGoogle(): Promise<{ user: unknown; idToken: string }> {
    const firebaseAuth = initFirebase()
    const provider = new GoogleAuthProvider()
    const result = await signInWithPopup(firebaseAuth, provider)
    const idToken = await result.user.getIdToken()
    return { user: result.user, idToken }
  }
  return { signInWithGoogle }
}
```

### Login Page (RUT + Google OAuth)

```vue
<!-- pages/login.vue -->
<template>
  <UCard>
    <UAuthForm
      title="Iniciar Sesion"
      description="Ingresa tus credenciales para acceder a {AppName}"
      :fields="fields"
      :loading="loading"
      :submit="{ label: 'Iniciar Sesion', color: 'primary' }"
      @submit="onSubmit"
    >
      <template #validation>
        <UAlert v-if="loginError" color="error" icon="lucide:circle-alert" :description="loginError" />
      </template>

      <template #footer>
        <div class="flex items-center gap-3 my-4">
          <div class="flex-1 border-t" />
          <span class="text-sm text-[var(--ui-text-muted)]">o</span>
          <div class="flex-1 border-t" />
        </div>

        <UButton block variant="outline" color="neutral" :loading="loadingGoogle" @click="loginWithGoogle">
          <template #leading>
            <!-- Google SVG icon (4-color) -->
            <svg width="18" height="18" viewBox="0 0 18 18">
              <path fill="#4285F4" d="M17.64 9.2c0-.637-.057-1.251-.164-1.84H9v3.481h4.844c-.209 1.125-.843 2.078-1.796 2.717v2.258h2.908c1.702-1.567 2.684-3.874 2.684-6.615z"/>
              <path fill="#34A853" d="M9 18c2.43 0 4.467-.806 5.96-2.184l-2.908-2.258c-.806.54-1.837.86-3.052.86-2.347 0-4.335-1.585-5.043-3.715H.957v2.332C2.438 15.983 5.482 18 9 18z"/>
              <path fill="#FBBC05" d="M3.957 10.713c-.18-.54-.282-1.117-.282-1.713s.102-1.173.282-1.713V4.955H.957C.348 6.173 0 7.55 0 9s.348 2.827.957 4.045l3-2.332z"/>
              <path fill="#EA4335" d="M9 3.58c1.321 0 2.508.454 3.44 1.345l2.582-2.58C13.463.891 11.426 0 9 0 5.482 0 2.438 2.017.957 4.955L3.957 7.287C4.665 5.157 6.653 3.58 9 3.58z"/>
            </svg>
          </template>
          Continuar con Google
        </UButton>
      </template>
    </UAuthForm>
  </UCard>
</template>
```

### Auth Layout

```vue
<!-- layouts/auth.vue -->
<template>
  <div class="min-h-screen bg-[var(--ui-bg-elevated)] flex flex-col items-center justify-center p-4">
    <div class="w-full max-w-md">
      <div class="flex flex-col items-center mb-8">
        <img src="/edutecnia/edutecnia_icon_transparent.png" alt="Edutecnia" class="h-16 mb-3">
        <img src="/edutecnia/edutecnia-logo.svg" alt="Edutecnia" class="h-6">
      </div>
      <slot />
    </div>
  </div>
</template>
```

## API Endpoints (Portal Auth)

| Method | Endpoint | Body | Description |
|--------|----------|------|-------------|
| POST | `/api/v1/portal/auth/login` | `{ rut, password }` | RUT login, sets SSO cookie |
| POST | `/api/v1/portal/auth/firebase_login` | `{ id_token }` | Google OAuth, sets SSO cookie |
| GET | `/api/v1/portal/auth/profile` | - | Fetch user profile (uses cookie) |
| DELETE | `/api/v1/portal/auth/logout` | - | Clear SSO cookie |

## Composable CRUD Pattern

```ts
// composables/use{Feature}.ts
export function useFeature() {
  const rhApi = useRhApi()  // or useApi() for non-RRHH
  const items = ref<Item[]>([])
  const loading = ref(false)
  const pagination = ref<Pagination>({ currentPage: 1, totalCount: 0, totalPages: 0, pageSize: 20 })

  async function fetchItems(params?: { page?: number; search?: string }) {
    loading.value = true
    try {
      const data = await rhApi.get<{ data: Item[]; meta: Pagination }>('/items', { params })
      items.value = data.data
      pagination.value = data.meta
    } finally {
      loading.value = false
    }
  }

  async function createItem(payload: Partial<Item>) { await rhApi.post('/items', { body: payload }) }
  async function updateItem(id: number, payload: Partial<Item>) { await rhApi.put(`/items/${id}`, { body: payload }) }
  async function deleteItem(id: number) { await rhApi.del(`/items/${id}`) }

  return { items, loading, pagination, fetchItems, createItem, updateItem, deleteItem }
}
```

## Modal Form Pattern (useOverlay)

```vue
<!-- components/{feature}/ItemFormModal.vue -->
<template>
  <UModal :title="isEdit ? 'Editar Item' : 'Crear Item'" :ui="{ footer: 'justify-end' }">
    <template #body>
      <UForm ref="formRef" :schema="schema" :state="state" @submit="onSubmit">
        <UFormField label="Nombre" name="name">
          <UInput v-model="state.name" />
        </UFormField>
      </UForm>
    </template>
    <template #footer>
      <UButton color="neutral" variant="outline" @click="emit('close', false)">Cancelar</UButton>
      <UButton :loading="saving" @click="formRef?.submit()">Guardar</UButton>
    </template>
  </UModal>
</template>
```

Usage with `useOverlay()`:

```ts
const overlay = useOverlay()
async function openForm(item?: Item) {
  const modal = overlay.create(ItemFormModal, { props: { item: item ?? null } })
  const saved = await modal.open()
  if (saved) fetchItems()
}
```

## Table + Pagination Pattern

```vue
<UCard :ui="{ body: 'p-0' }">
  <UTable :columns="columns" :rows="items" :loading="loading" />
  <template #footer>
    <div class="flex items-center justify-between px-4 py-3">
      <span class="text-sm text-[var(--ui-text-muted)]">{{ pagination.totalCount }} registros</span>
      <UPagination v-model="currentPage" :total="pagination.totalCount" :items-per-page="pagination.pageSize" />
    </div>
  </template>
</UCard>
```

## Establishment Picker

Si la app opera scoped por establecimiento (RRHH, Config, SED, Accounting, Docs), debe implementar un **picker full-screen** post-login para usuarios con multiples establecimientos. Ver skill `establishment-picker` para el patron completo.

## Key Conventions

- **Backend port**: `localhost:3040` (Rails API)
- **Dev ports**: 8041 (backoffice), 8042 (docs), 8043 (config), 8044 (rrhh), 8045 (accounting), 8046 (sed), 8047 (switcher)
- **Global size `xl`**: All Nuxt UI components default to `size="xl"` via `nuxt.config.ts`. Only override for icon/secondary buttons (`size="xs"`, `size="sm"`)
- **Snake_case payloads**: Always send snake_case keys to Rails backend
- **SSO cookie**: `edutecnia_token` with `credentials: 'include'`
- **Firebase**: Lazy-loaded singleton, only for Google OAuth popup
- **Types**: `interface` for objects, `type` for unions, no `Interface` suffix
- **Composables over Pinia** for feature-specific state
- **UTabs**: Use `slot` property with `as const` for typed template slots
- **Zod validation**: For form schemas in modal components
- **UAuthForm**: Use `:submit` prop for button, `#footer` slot for Google button, `#validation` slot for errors
- **USelect items**: Never use `value: ''` (empty string) — causes SSR 500 error. Use a sentinel value like `'all'` and filter before API call

## Gotchas Conocidos (Nuxt UI v4 + Tailwind v4)

### 1. Colores custom en @theme se pierden (CRITICO)
Tailwind v4 tree-shakea variables CSS en `@theme {}` si ninguna utility class las usa directamente. Nuxt UI genera `var(--color-edutecnia-50, )` con fallback **vacio** para colores custom. Resultado: botones con `bg-primary` quedan transparentes (invisibles).

**Fix**: Usar `:root {}` en vez de `@theme {}` en `main.css`.

### 2. UAuthForm @submit emite { data: T }, NO T directamente
```ts
// MAL — crashea con "Cannot read properties of undefined (reading 'replace')"
async function onSubmit(data: { rut: string, password: string }) {
  await authStore.login(data.rut, data.password) // data.rut es undefined!
}

// BIEN — FormSubmitEvent envuelve en { data: T }
async function onSubmit(event: { data: { rut: string, password: string } }) {
  await authStore.login(event.data.rut, event.data.password)
}
```

### 3. USelect no acepta value: '' (string vacio)
Nuxt UI v4 lanza error 500: `<SelectItem /> must have a value prop that is not an empty string`.

```ts
// MAL
const items = [{ value: '', label: 'Todos' }, ...options]

// BIEN — usar placeholder en vez de item con value vacio
<USelect :items="options" placeholder="Todos" class="w-full" />
```

### 4. Formato de iconos: lucide:name (NO i-lucide-name)
```ts
// MAL (formato Nuxt UI v2/UnoCSS)
icon: 'i-lucide-check-circle'

// BIEN (formato Nuxt UI v4 + @nuxt/icon)
icon: 'lucide:check-circle'
```

### 5. Inputs no toman ancho completo
`UInput`, `UTextarea`, `USelect` NO se expanden automaticamente dentro de `UFormField`. Siempre agregar `class="w-full"`.

### 6. Auth middleware: usar hasToken para SSR
`authStore.isAuthenticated` solo funciona client-side (requiere `initAuth()`). Para checks en SSR usar `authStore.hasToken` que lee `useCookie()` desde los request headers.

```ts
// En middleware/auth.global.ts — redirect /login si ya autenticado
if (to.path === '/login') {
  // hasToken funciona en SSR (useCookie lee headers) — previene hydration mismatch
  if (authStore.isAuthenticated || authStore.hasToken) return navigateTo('/')
}
```

### 7. app.vue: NO duplicar initAuth
`initAuth()` debe llamarse UNA sola vez en `app.vue` `onMounted`. El middleware NO debe llamar `initAuth` por separado, ya que causa race conditions.
