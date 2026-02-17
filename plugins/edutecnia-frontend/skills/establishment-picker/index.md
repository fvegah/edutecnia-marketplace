---
name: establishment-picker
description: Full-screen establishment picker pattern for multi-tenant Nuxt 4 apps. Forces explicit selection after login when user has access to multiple establishments.
---

# Flujo de Seleccion de Establecimiento (Nuxt 4)

> **Alcance:** Solo aplica a proyectos del ecosistema Edutecnia. No usar en otros proyectos.

## Descripcion
Patron para forzar la seleccion de establecimiento despues del login cuando el usuario tiene acceso a mas de uno. Aplica a todos los proyectos Nuxt 4 de Edutecnia que operan scoped por establecimiento: edutecnia-rrhh, edutecnia-config, edutecnia-sed, edutecnia-accounting, edutecnia-docs.

## Contexto del Problema
Cada app Nuxt 4 opera scoped a un establecimiento (las APIs usan `/establishments/{id}/...`). Actualmente, `useEstablishment()` auto-selecciona el primero o el guardado en localStorage sin que el usuario lo confirme. Cuando un usuario tiene acceso a multiples colegios, debe elegir explicitamente cual quiere usar antes de ver el dashboard.

## Referencia: Implementacion en Portal (Vue 2)
El Portal ya implementa este flujo en `edutecnia-front/`:
- **Vista**: `src/views/SelectEstablishment.vue` — full-screen picker
- **Store action**: `establishment/selectAndLoadEstablishment` — centraliza limpieza + carga
- **Login**: `navigateAfterLogin()` decide si mostrar picker o ir al dashboard
- **Breadcrumb**: chip con nombre del establecimiento, clickeable para volver al picker
- **Router guard**: `/select-establishment` es ruta autenticada pero no redirige a dashboard

## Flujo Requerido

```
Login (SSO cookie)
  |
  v
fetchProfile() → user.userEstablishments[]
  |
  v
navigateAfterLogin()
  |--- 1 establecimiento → seleccionar automaticamente → /  (dashboard)
  |--- >1 establecimientos + localStorage tiene seleccion previa → /  (dashboard)
  |--- >1 establecimientos + sin seleccion previa → /select-establishment  (PICKER)
                                                        |
                                                     usuario elige
                                                        |
                                                     selectEstablishment(id)
                                                        |
                                                     /  (dashboard)
```

## Archivos a Crear/Modificar

### 1. `pages/select-establishment.vue` (NUEVA)

Pagina full-screen con layout `auth` (sin sidebar/navbar). Muestra lista de establecimientos como cards clickeables.

```vue
<script setup lang="ts">
definePageMeta({
  layout: 'auth',
})

const authStore = useAuthStore()
const { establishments, selectEstablishment, establishmentId } = useEstablishment()

const loading = ref<number | null>(null)

// Safety net: si solo tiene 1, ir directo
onMounted(() => {
  if (establishments.value.length <= 1) {
    navigateTo('/')
  }
})

async function select(est: { establishmentId: number }) {
  if (loading.value) return
  loading.value = est.establishmentId
  selectEstablishment(est.establishmentId)
  await navigateTo('/')
}

async function logout() {
  await authStore.logout()
  await navigateTo('/login')
}
</script>

<template>
  <UCard class="w-full max-w-md mx-auto">
    <!-- Logo -->
    <template #header>
      <div class="flex flex-col items-center gap-2">
        <img src="/edutecnia/edutecnia_icon_transparent.png" alt="Edutecnia" class="h-16">
        <img src="/edutecnia/edutecnia-logo.svg" alt="Edutecnia" class="h-6">
      </div>
    </template>

    <!-- Saludo + lista -->
    <div class="space-y-4">
      <div class="text-center">
        <h2 class="text-xl font-semibold capitalize">
          Hola, {{ (authStore.user as any)?.name }}
        </h2>
        <p class="text-sm text-[var(--ui-text-muted)]">
          Selecciona tu establecimiento
        </p>
      </div>

      <div class="space-y-2 max-h-80 overflow-y-auto">
        <button
          v-for="est in establishments"
          :key="est.establishmentId"
          class="w-full flex items-center gap-3 p-3 rounded-lg border border-[var(--ui-border)]
                 hover:border-[var(--ui-primary)] hover:bg-[var(--ui-bg-elevated)]
                 transition-all text-left"
          :class="{ 'opacity-60 pointer-events-none': loading && loading !== est.establishmentId }"
          @click="select(est)"
        >
          <div class="flex-shrink-0 size-10 rounded-lg bg-primary-50 dark:bg-primary-950/40
                      flex items-center justify-center text-primary-500">
            <UIcon name="lucide:building" class="size-5" />
          </div>
          <div class="flex-1 min-w-0">
            <p class="font-medium truncate">{{ est.establishment?.name }}</p>
            <p class="text-xs text-[var(--ui-text-muted)]">{{ est.role }}</p>
          </div>
          <UIcon
            v-if="loading === est.establishmentId"
            name="lucide:loader"
            class="size-4 animate-spin text-[var(--ui-text-muted)]"
          />
          <UIcon v-else name="lucide:chevron-right" class="size-4 text-[var(--ui-text-muted)]" />
        </button>
      </div>
    </div>

    <template #footer>
      <div class="text-center">
        <UButton
          label="Cerrar Sesion"
          variant="link"
          color="neutral"
          size="sm"
          @click="logout"
        />
      </div>
    </template>
  </UCard>
</template>
```

### 2. Modificar `pages/login.vue`

Reemplazar `navigateTo('/')` con logica de decision:

```vue
<script setup lang="ts">
// ... campos y estado existentes ...

async function onSubmit(data: { rut: string, password: string }) {
  loginError.value = ''
  loading.value = true
  try {
    await authStore.login(data.rut, data.password)
    await authStore.fetchProfile()
    navigateAfterLogin()
  }
  catch (error: unknown) {
    const err = error as { message?: string }
    loginError.value = err?.message || 'Error al iniciar sesion'
  }
  finally {
    loading.value = false
  }
}

function navigateAfterLogin() {
  const user = authStore.user as Record<string, unknown> | null
  const userEstablishments = (user?.userEstablishments ?? []) as { establishmentId: number }[]
  const hasStoredEstablishment = !!localStorage.getItem('edutecnia_selected_establishment_id')

  if (userEstablishments.length > 1 && !hasStoredEstablishment) {
    navigateTo('/select-establishment')
  } else {
    navigateTo('/')
  }
}
</script>
```

### 3. Modificar `middleware/auth.global.ts`

Agregar `/select-establishment` como ruta protegida pero permitida:

```ts
export default defineNuxtRouteMiddleware(async (to) => {
  const authStore = useAuthStore()
  const publicRoutes = ['/login', '/logout']

  if (publicRoutes.includes(to.path)) return

  // Already authenticated
  if (authStore.isAuthenticated) return

  // SSO check only works client-side
  if (import.meta.client) {
    await authStore.initAuth()
    if (authStore.user) return
    return navigateTo('/login')
  }
})
```

No requiere cambios: `/select-establishment` ya es una ruta protegida normal (requiere auth, no esta en publicRoutes). El middleware la deja pasar si el usuario esta autenticado.

### 4. Agregar indicador en AppBar o layout

En el layout `default.vue` o en `AppBar.vue`, mostrar el nombre del establecimiento seleccionado con opcion de cambiar:

```vue
<!-- En el sidebar header o en AppBar -->
<script setup>
const { establishmentName, establishments } = useEstablishment()
const hasMultiple = computed(() => establishments.value.length > 1)
</script>

<!-- Dentro de UDashboardNavbar o sidebar header -->
<UButton
  v-if="establishmentName"
  :label="establishmentName"
  icon="lucide:building"
  :trailing-icon="hasMultiple ? 'lucide:chevron-down' : undefined"
  variant="ghost"
  color="neutral"
  size="sm"
  class="max-w-48 truncate"
  :class="{ 'cursor-default': !hasMultiple }"
  @click="hasMultiple && navigateTo('/select-establishment')"
/>
```

## Edge Cases

| Caso | Comportamiento |
|------|---------------|
| 1 solo establecimiento | `navigateAfterLogin()` va directo a `/`. `onMounted` del picker tambien redirige a `/` |
| localStorage con seleccion previa | `navigateAfterLogin()` va directo a `/`. `useEstablishment()` restaura la seleccion guardada |
| Establecimiento guardado ya no existe | `useEstablishment.initEstablishment()` no lo encuentra en la lista → fallback al primero |
| SSO auto-login (sin pasar por login) | El middleware autentica → `useEstablishment` auto-inicializa → todo funciona sin picker |
| Deep link a pagina especifica | Middleware verifica auth → si OK, la pagina carga normal con `useEstablishment` ya inicializado |
| Logout | `authStore.logout()` limpia user. localStorage de establecimiento persiste para siguiente login |
| Re-login despues de seleccion previa | localStorage tiene el ID → `navigateAfterLogin()` va directo a `/` |

## Que NO Hacer

- **NO mostrar picker si tiene 1 solo establecimiento** — ir directo al dashboard
- **NO borrar localStorage del establecimiento en logout** — debe persistir para comodidad del re-login
- **NO usar modal/dropdown para cambiar establecimiento** — debe ser pagina completa (invasiva) para asegurar que el usuario es consciente del cambio de contexto
- **NO cargar datos del dashboard antes de seleccionar establecimiento** — las APIs fallan sin establishmentId

## Relacion con useEstablishment()

El composable `useEstablishment()` ya existe en los proyectos Nuxt y maneja:
- Persistencia en localStorage
- Auto-inicializacion desde profile
- Computed `establishmentId` para las APIs

El picker **no reemplaza** `useEstablishment()`. Solo le da una UI para que el usuario elija de forma explicita antes de que el composable auto-seleccione. Despues de que el usuario elige en el picker, `selectEstablishment(id)` del composable se encarga de persistir.

## Checklist de Implementacion

- [ ] Crear `pages/select-establishment.vue` con layout `auth`
- [ ] Modificar `pages/login.vue`: `navigateAfterLogin()` decide picker vs dashboard
- [ ] Verificar que `middleware/auth.global.ts` permite `/select-establishment` (ya deberia)
- [ ] Agregar indicador de establecimiento en AppBar/sidebar con link al picker
- [ ] Probar: login multi-establecimiento sin localStorage → ver picker
- [ ] Probar: login single-establecimiento → dashboard directo
- [ ] Probar: re-login con seleccion previa → dashboard directo
- [ ] Probar: click en nombre de establecimiento → vuelve al picker
- [ ] Probar: dark mode en el picker
