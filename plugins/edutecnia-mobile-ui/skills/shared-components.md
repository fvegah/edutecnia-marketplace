---
name: mobile-ui-standard
description: Standard UI patterns for Edutecnia mobile apps (Ionic Vue + Capacitor). Covers dark mode, page structure, modals, forms, components, icons, and styling for parents, teachers, and students apps.
---

# Edutecnia UI Mobile Standard (Ionic Vue + Capacitor)

> **Alcance:** Solo aplica a proyectos del ecosistema Edutecnia. No usar en otros proyectos.

## Descripcion
Estandar de UI para las apps moviles de Edutecnia. Define patrones, componentes y convenciones para construir interfaces consistentes usando Ionic Vue 8 + Vue 3 + Capacitor 7 + Vite. Aplica a: edutecnia-parents-app, edutecnia-teachers-app, y cualquier proyecto Ionic Vue nuevo.

## Stack Obligatorio
- **Framework:** Vue 3.5+ con Composition API (`<script setup>`)
- **UI Library:** Ionic Vue 8.4+ (SIEMPRE usar componentes Ionic)
- **Mobile Bridge:** Capacitor 7.x (iOS + Android)
- **Build:** Vite 6+
- **HTTP:** Axios
- **Icons:** ionicons (ios/md variants)
- **Messaging:** Firebase 11+
- **Storage:** @capacitor/preferences
- **Testing:** Vitest + Cypress

## Colores Edutecnia

```css
/* src/theme/base.css - Paleta primaria */
:root {
  --ion-color-primary: #ff7816;
  --ion-color-primary-rgb: 255,120,22;
  --ion-color-primary-contrast: #000000;
  --ion-color-primary-contrast-rgb: 0,0,0;
  --ion-color-primary-shade: #e06a13;
  --ion-color-primary-tint: #ff862d;
}
```

### Colores semanticos (dark mode)
```css
body.dark {
  --ion-color-secondary: #50c8ff;
  --ion-color-tertiary: #6a64ff;
  --ion-color-success: #2fdf75;
  --ion-color-warning: #ffd534;
  --ion-color-danger: #ff4961;
  --ion-color-dark: #f4f5f8;       /* invertido */
  --ion-color-medium: #989aa2;
  --ion-color-light: #222428;      /* invertido */
}
```

### iOS Dark
```css
.ios body.dark {
  --ion-background-color: #000000;
  --ion-text-color: #ffffff;
  --ion-item-background: #1c1c1d;
  --ion-card-background: #1c1c1d;
  /* Step scale: #0d0d0d (50) → #f2f2f2 (950) */
}
.ios body.dark ion-modal {
  --ion-background-color: var(--ion-color-step-100);
  --ion-toolbar-background: var(--ion-color-step-150);
  --ion-toolbar-border-color: var(--ion-color-step-250);
}
```

### MD Dark
```css
.md body.dark {
  --ion-background-color: #121212;
  --ion-text-color: #ffffff;
  --ion-border-color: #222222;
  --ion-item-background: #1e1e1e;
  --ion-toolbar-background: #1f1f1f;
  --ion-tab-bar-background: #1f1f1f;
  --ion-card-background: #1e1e1e;
}
```

### Colores ad-hoc usados en la app
```
#112327         → Sombra login card (dark teal, light mode)
#3498db         → Calendario: 1 evento (azul)
#e74c3c         → Calendario: >1 evento (rojo)
#2980b9         → Calendario: borde evento
rgba(91,145,59,0.1) → Fila de recreo, divider de observaciones (verde claro)
#ff6b6b         → Badge "development" (rojo)
#ffd43b         → Badge "staging" (amarillo)
```

## Estructura de Proyecto

```
src/
├── assets/                  # SVGs, imagenes (edutecnia-logo.svg, avatar.svg)
├── components/
│   ├── attendance/          # Modal de asistencia
│   ├── auth/                # Cambio de contrasena
│   ├── classmates/          # Companeros
│   ├── common/              # EduLogo, SelectStudent, LoadingSkeleton
│   ├── configuration/       # Perfil, About, Politicas
│   ├── grades/              # Calificaciones (list, modal)
│   ├── layout/              # AppLayout, AppMenu, AppTopBar
│   ├── notes/               # Observaciones
│   └── schedule/            # Horario
├── router/                  # Vue Router con guards
├── services/                # API calls (Axios)
├── store/                   # Estado (Capacitor Preferences)
├── theme/                   # CSS: base.css, dark.css, auth/login.css
├── utils/                   # Helpers (theme.js, etc.)
├── views/                   # Paginas principales
│   └── session/             # Login, Recover, ChangePassword
├── main.ts                  # Entry point
└── App.vue                  # Root <ion-app>
```

## Dark Mode

Implementado via clase CSS en `<body>`, NO media query. Tres modos: `light`, `dark`, `system`.

```javascript
// src/utils/theme.js
export const applyTheme = (themeMode) => {
  const prefersDark = window.matchMedia('(prefers-color-scheme: dark)');
  const mode = themeMode || 'system';
  if (mode === 'system') toggleDarkTheme(prefersDark.matches);
  else if (mode === 'dark') toggleDarkTheme(true);
  else toggleDarkTheme(false);
};
const toggleDarkTheme = (shouldAdd) => {
  document.body.classList.toggle('dark', shouldAdd);
};
```

Selector de tema en ConfigurationPage:
```html
<ion-segment :scrollable="true" v-model="themeMode">
  <ion-segment-button value="light"><ion-icon :icon="sunny"></ion-icon></ion-segment-button>
  <ion-segment-button value="dark"><ion-icon :icon="moon"></ion-icon></ion-segment-button>
  <ion-segment-button value="system">AUTO</ion-segment-button>
</ion-segment>
```

Selectores CSS para dark mode: `body.dark`, `.ios body.dark`, `.md body.dark`.

## Patron: AppLayout (Split Pane)

```html
<!-- src/components/layout/AppLayout.vue -->
<template>
  <ion-split-pane content-id="main-content">
    <ion-menu content-id="main-content" type="overlay">
      <app-menu></app-menu>
    </ion-menu>
    <div class="ion-page" id="main-content">
      <app-top-bar></app-top-bar>
      <ion-content>
        <ion-router-outlet></ion-router-outlet>
      </ion-content>
    </div>
  </ion-split-pane>
</template>
```

- Split pane con side menu `type="overlay"`
- Contenido protegido dentro de `ion-router-outlet`

## AppTopBar

```html
<ion-header :translucent="true">
  <ion-toolbar>
    <ion-buttons slot="start">
      <ion-menu-button color="primary"></ion-menu-button>
    </ion-buttons>
    <ion-title>Edutecnia | Apoderados</ion-title>
  </ion-toolbar>
</ion-header>
```

- Header: `translucent`
- Menu button: `color="primary"` (naranja)
- Titulo: `"Edutecnia | NombreApp"` (ej: "Edutecnia | Apoderados", "Edutecnia | Docentes")

## AppMenu (Side Drawer)

Logo en la parte superior, dos secciones de items (navegacion principal + configuracion).

```javascript
const appPages = [
  { id: 1, title: 'Inicio',         url: '/app/inicio',         iosIcon: homeOutline,         mdIcon: homeSharp },
  { id: 2, title: 'Agenda',         url: '/app/agenda',         iosIcon: bookOutline,         mdIcon: bookSharp },
  { id: 3, title: 'Horario',        url: '/app/horario',        iosIcon: calendarOutline,     mdIcon: calendarSharp },
  { id: 4, title: 'Calificaciones', url: '/app/calificaciones', iosIcon: nutritionOutline,    mdIcon: nutritionSharp },
  // ...
];
const otherPages = [
  { id: 9, title: 'Configuraciones', url: '/app/configuraciones', iosIcon: settingsOutline, mdIcon: settingsSharp },
];
```

**Estilos clave del menu:**
```css
/* MD selected state */
ion-menu.md ion-item.selected {
  --background: rgba(var(--ion-color-primary-rgb), 0.14);
}
ion-menu.md ion-item.selected ion-icon { color: var(--ion-color-primary); }
ion-menu.md ion-item ion-icon { color: #616e7e; }
ion-menu.md ion-item { --padding-start: 10px; --padding-end: 10px; border-radius: 4px; font-size: 14px; }
ion-menu.md ion-item ion-label { font-weight: 500; }

/* iOS selected state */
ion-menu.ios ion-item.selected ion-icon { color: var(--ion-color-primary); }
ion-menu.ios ion-item ion-icon { font-size: 24px; color: #73849a; }
ion-menu.ios ion-item { --padding-start: 16px; --padding-end: 16px; --min-height: 50px; }

/* Global selected */
ion-item.selected { --color: var(--ion-color-primary); }

/* Logo container */
.logo-container { padding-top: var(--ion-safe-area-top); padding-bottom: 1rem; }
.logo-container img { max-width: 180px; margin: 0 auto; display: block; }
```

## Patron: Pagina Estandar

Todas las paginas protegidas siguen esta estructura exacta:

```html
<template>
  <ion-page>
    <ion-content :fullscreen="true">
      <div id="container">

        <!-- Pull-to-Refresh (siempre al inicio del container) -->
        <ion-refresher slot="fixed" @ionRefresh="handleRefresh($event)"
          :pull-factor="0.5" :pull-min="100" :pull-max="200">
          <ion-refresher-content></ion-refresher-content>
        </ion-refresher>

        <!-- Contenido cuando esta cargado -->
        <ion-grid v-if="!loading">
          <ion-row>
            <select-student @on-select-student="onSelectStudent"></select-student>
          </ion-row>
          <!-- ... contenido de la pagina ... -->
        </ion-grid>

        <!-- Estado de carga -->
        <ion-grid v-else>
          <loading-skeleton/>
        </ion-grid>

      </div>
    </ion-content>
  </ion-page>
</template>
```

Convenciones:
- `ion-content :fullscreen="true"` en cada pagina
- Todo envuelto en `<div id="container">`
- `ion-refresher` con `slot="fixed"`, `:pull-factor="0.5"`, `:pull-min="100"`, `:pull-max="200"`
- `v-if="!loading"` / `v-else` con `<loading-skeleton/>` como fallback
- `<select-student>` como primera fila del grid en todas las paginas con datos

## Pull-to-Refresh

Siempre el mismo template:
```html
<ion-refresher slot="fixed" @ionRefresh="handleRefresh($event)"
  :pull-factor="0.5" :pull-min="100" :pull-max="200">
  <ion-refresher-content></ion-refresher-content>
</ion-refresher>
```

Handler:
```javascript
const handleRefresh = (event) => {
  fetchData().finally(() => {
    event.target.complete();
  });
};
```

## Componente: EduLogo

```html
<!-- src/components/common/EduLogo.vue -->
<div class="edu-logo" :class="[`edu-logo--${size}`]">
  <img src="@/assets/edutecnia-logo.svg" alt="Edutecnia" class="edu-logo__image" />
  <span v-if="showEnvironment && environmentLabel"
        class="edu-logo__badge" :class="[`edu-logo__badge--${environment}`]">
    {{ environmentLabel }}
  </span>
</div>
```

Props: `size: 'small' | 'medium' | 'large'` (default `'medium'`), `showEnvironment: boolean` (default `true`).

```css
.edu-logo--small  .edu-logo__image { max-width: 120px; }
.edu-logo--medium .edu-logo__image { max-width: 180px; }
.edu-logo--large  .edu-logo__image { max-width: 240px; }

.edu-logo__badge { font-size: 10px; font-weight: 700; letter-spacing: 1px; padding: 2px 8px; border-radius: 4px; text-transform: uppercase; }
.edu-logo__badge--development { background-color: #ff6b6b; color: white; }
.edu-logo__badge--staging     { background-color: #ffd43b; color: #333; }
```

Deteccion de entorno via `VITE_API_URL`: `localhost` → development, `staging` → staging, otro → production (sin badge).

Uso: `<EduLogo size="medium" />` en menu, `<EduLogo size="large" />` en login.

## Componente: SelectStudent

Selector tipo acordeon que muestra el estudiante activo y permite cambiar entre hijos.

```html
<div class="student-selector">
  <!-- Barra del estudiante seleccionado (siempre visible) -->
  <ion-item class="selected-student-bar" button @click="isAccordionOpen = !isAccordionOpen">
    <ion-avatar slot="start">...</ion-avatar>
    <ion-label>
      <h2>{{ name }}</h2>
      <p>{{ course_name }}</p>
      <p class="establishment-info">{{ establishment.name }}</p>
    </ion-label>
    <ion-avatar slot="end" class="establishment-logo">...</ion-avatar>
  </ion-item>

  <!-- Lista acordeon -->
  <div class="students-accordion" :class="{ 'open': isAccordionOpen }">
    <ion-list>
      <ion-item v-for="student in otherStudents" ...>
      </ion-item>
    </ion-list>
  </div>
</div>
```

Emite: `@on-select-student`

**CSS clave:**
```css
.selected-student-bar {
  --background: var(--ion-color-light);
  border-radius: 8px; margin: 0.5rem;
}
.selected-student-bar ion-avatar { width: 40px; height: 40px; }
.selected-student-bar h2 { font-size: 1rem; font-weight: 600; }
.selected-student-bar p { font-size: 0.85rem; color: var(--ion-color-medium); }

.students-accordion {
  max-height: 0; overflow: hidden;
  transition: max-height 0.3s ease-out;
}
.students-accordion.open {
  max-height: 300px; overflow-y: auto;
  box-shadow: 0 2px 8px rgba(0,0,0,0.1);
}
.students-accordion ion-item.selected {
  --background: rgba(var(--ion-color-primary-rgb), 0.1);
}
```

## Componente: LoadingSkeleton

```html
<!-- src/components/common/loadingSkeleton.vue -->
<ion-row style="margin-top: 100px;">
  <ion-col>
    <ion-list>
      <ion-list-header>
        <ion-skeleton-text :animated="true" style="width: 80px"></ion-skeleton-text>
      </ion-list-header>
      <!-- 3 filas identicas con thumbnail + 3 lineas de texto -->
      <ion-item>
        <ion-thumbnail slot="start">
          <ion-skeleton-text :animated="true"></ion-skeleton-text>
        </ion-thumbnail>
        <ion-label>
          <h3><ion-skeleton-text :animated="true" style="width: 80%;"></ion-skeleton-text></h3>
          <p><ion-skeleton-text :animated="true" style="width: 60%;"></ion-skeleton-text></p>
          <p><ion-skeleton-text :animated="true" style="width: 30%;"></ion-skeleton-text></p>
        </ion-label>
      </ion-item>
      <!-- repetir 2 veces mas -->
    </ion-list>
  </ion-col>
</ion-row>
```

- `margin-top: 100px` para separar del header
- 3 filas con thumbnail + 3 lineas (80%, 60%, 30%)
- Siempre `:animated="true"`

### Spinner inline (para cargas secundarias)
```html
<ion-row class="ion-justify-content-center ion-align-items-center" style="height: 60vh;">
  <ion-col class="ion-text-center">
    <ion-spinner name="crescent" />
    <div>Cargando...</div>
  </ion-col>
</ion-row>
```

Spinner variant: siempre `name="crescent"`.

## Modales / Sheets

Patron principal: `modalController.create()` con `initialBreakpoint: 0.85`.

### Abrir modal (programatico)
```javascript
import { modalController } from '@ionic/vue';

const modal = await modalController.create({
  component: SomeModal,
  componentProps: { title: '...', details: item },
  initialBreakpoint: 0.85,
});
modal.present();
```

### Cerrar modal (desde dentro)
```javascript
const cancel = () => modalController.dismiss(null, 'cancel');
```

### Template del modal
```html
<!-- Sin <ion-page> wrapper — empiezan con <ion-header> -->
<template>
  <ion-header>
    <ion-toolbar>
      <ion-title>{{ title }}</ion-title>
      <ion-buttons slot="end">
        <ion-button color="medium" @click="cancel">Cerrar</ion-button>
      </ion-buttons>
    </ion-toolbar>
  </ion-header>
  <ion-content class="ion-padding">
    <!-- contenido -->
  </ion-content>
</template>
```

### Modal con `<ion-modal>` inline (alternativa)
```html
<ion-modal :is-open="true" @didDismiss="$emit('close')" :can-dismiss="true">
  <ion-header>
    <ion-toolbar>
      <ion-buttons slot="start">
        <ion-button @click="$emit('close')">
          <ion-icon name="arrow-back"></ion-icon>Volver
        </ion-button>
      </ion-buttons>
      <ion-title>Detalle</ion-title>
      <ion-buttons slot="end">
        <ion-button @click="$emit('close')">
          <ion-icon name="close"></ion-icon>
        </ion-button>
      </ion-buttons>
    </ion-toolbar>
  </ion-header>
  <ion-content class="ion-padding">...</ion-content>
</ion-modal>
```

## Formularios

### Patron de input
```html
<ion-input
  v-model="email"
  type="email"
  label-placement="stacked"
  label="Correo electronico"
  placeholder="ejemplo@correo.com"
  :error="!!errors.email"
  :error-text="errors.email"
>
  <ion-icon slot="start" :ios="mailOutline" :md="mailSharp"></ion-icon>
</ion-input>
```

Convenciones:
- `label-placement="stacked"` — siempre stacked (label arriba del input)
- Iconos en `slot="start"` para indicar tipo de campo
- Toggle password en `slot="end"` (`eyeOutline` / `eyeOffOutline`)
- `ion-input-password-toggle` en login, icono manual en cambio de contrasena
- `:clear-input="true"` en campos de contrasena (cambio de contrasena)
- Validacion: `:class="{ 'ion-invalid': errorRef }"` en `ion-item`

### Boton submit
```html
<ion-button type="submit" expand="block" :disabled="isLoading">
  <ion-spinner v-if="isLoading" name="crescent"></ion-spinner>
  <span v-else>Ingresar</span>
</ion-button>
```

### Toast de notificacion
```javascript
const toast = await toastController.create({
  message: 'Contrasena actualizada exitosamente',
  duration: 2000,       // 2000-3000ms
  color: 'success',     // o 'danger'
  position: 'top',
});
await toast.present();
```

### Login Card
```html
<ion-card class="login-card">
  <div class="login-img-logo">
    <EduLogo size="large" />
  </div>
  <ion-card-header>
    <ion-card-subtitle style="font-style: italic;">Apoderados</ion-card-subtitle>
    <ion-card-title>Iniciar Sesion</ion-card-title>
  </ion-card-header>
  <ion-card-content>
    <form @submit.prevent="handleLogIn">...</form>
  </ion-card-content>
</ion-card>
```

```css
.login-card { max-width: 400px; margin: 2rem auto; padding: 1rem;
  box-shadow: 4px 4px 0 2px #112327; border-radius: 8px; }
.ios body.dark .login-card,
.md body.dark .login-card { box-shadow: 4px 4px 0 2px var(--ion-color-primary); }
```

## Listas e Items

### Lista agrupada con dividers
```html
<ion-list :inset="true">
  <ion-list-header>
    <ion-label>Titulo de Seccion</ion-label>
  </ion-list-header>
  <ion-item-group>
    <ion-item-divider>
      <ion-label>Encabezado de Grupo</ion-label>
    </ion-item-divider>
    <ion-item lines="full">
      <ion-label class="ion-text-wrap">
        <span class="label-title">Nombre del Campo</span>
        <span class="label-content">Valor del Campo</span>
      </ion-label>
    </ion-item>
  </ion-item-group>
</ion-list>
```

```css
.label-title { display: block; color: var(--ion-color-medium); font-size: 0.85rem; margin-bottom: 0.25rem; }
.label-content { display: block; color: var(--ion-color-dark); font-size: 1rem; line-height: 1.4; }
ion-item-divider { --background: var(--ion-color-light); --padding-top: 0.75rem; --padding-bottom: 0.75rem; }
ion-list { margin: 1rem; border-radius: 8px; }
ion-item { --padding-start: 1rem; --padding-end: 1rem; }
```

## Segmentos Scrollable (Tabs Horizontales)

```html
<!-- Periodos dinamicos (GradesPage) -->
<ion-segment :scrollable="true" v-model="selectedPeriod">
  <ion-segment-button v-for="period in subPeriods" :key="period.id"
    :value="period.id" content-id="resume">
    {{ period.name }}
  </ion-segment-button>
</ion-segment>
```

- Siempre `:scrollable="true"`
- `content-id` en button enlaza con `id` en `ion-segment-content`

## Iconos (ionicons)

Importar por nombre, patron dual iOS/MD:

```javascript
import {
  homeOutline, homeSharp,
  bookOutline, bookSharp,
  calendarOutline, calendarSharp,
  settingsOutline, settingsSharp,
  mailOutline, mailSharp,
  eyeOutline, eyeOffOutline,
  sunny, moon,
} from 'ionicons/icons';
```

Renderizar con variantes de plataforma:
```html
<ion-icon :ios="homeOutline" :md="homeSharp"></ion-icon>
```

## Tipografia y Espaciado

### Jerarquia de tamanos
```
1.5rem  → Encabezado de perfil (h2)
1rem    → Titulo item, contenido principal
0.9rem  → Texto secundario, error messages
0.85rem → Label-title, subtitulo
0.75rem → Establishment info (italic)
```

### Border radius
```
4px  → Menu items (MD), badge de entorno
8px  → Cards, listas, student bar, botones, login card
50%  → Avatares
```

## Avatar Sizes

```
100px × 100px  → Perfil
80px × 80px    → Detalle de companero
40px × 40px    → Student selector bar, lista
36px × 36px    → Student selector accordion, responsive
32px × 32px    → Logo de establecimiento
```

Default avatar: `@/assets/avatar.svg`

## Rutas

```
/                               → redirect a /login
/login                          → LogInPage (publica)
/auth/recuperar_contrasena      → RecoverPasswordPage (publica)
/auth/cambiar_contrasena        → ChangePasswordPage (publica)
/app                            → AppLayout (protegida, redirect a /app/inicio)
  /app/inicio                   → HomePage
  /app/agenda                   → AgendaPage
  /app/horario                  → SchedulePage
  /app/calificaciones           → GradesPage
  /app/observaciones            → NotesPage
  /app/asistencia               → AttendancePage
  /app/companeros               → ClassmatesPage
  /app/configuraciones          → ConfigurationPage
/:pathMatch(.*)*                → NotFoundPage
```

Auth guard: verifica `getIsAuthenticated()` de Capacitor Preferences. Si autenticado, llama `authService.getProfile()` para refrescar datos.

## Valores Clave (Referencia Rapida)

| Valor | Uso |
|---|---|
| `#ff7816` | Color primario (naranja) |
| `#e06a13` | Primary shade |
| `#ff862d` | Primary tint |
| `0.85` | initialBreakpoint de modales |
| `576px` | Unico breakpoint responsive |
| `8px` | Border-radius estandar |
| `crescent` | Variante de spinner |
| `0.3s ease-out` | Animacion del acordeon |
| `300px` | Max-height del acordeon abierto |
| `pull-factor 0.5, min 100, max 200` | Refresher config |
| `2000-3000ms` | Duracion de toasts |

## Reglas Estrictas

1. **NO usar Vuetify** — Solo componentes Ionic
2. **NO usar SCSS en archivos nuevos** — Solo CSS plano con variables Ionic
3. **NO usar Tailwind** — Solo utilidades CSS de Ionic y CSS custom
4. **NO usar Options API** — Solo Composition API con `<script setup>`
5. **NO usar Pinia/Vuex** — Usar `ref()` + Capacitor Preferences
6. **NO usar Lucide** — Solo ionicons (variantes ios/md)
7. **SIEMPRE** usar componentes Ionic (ion-button, ion-input, ion-list, etc.)
8. **SIEMPRE** `label-placement="stacked"` en inputs
9. **SIEMPRE** `initialBreakpoint: 0.85` en modales con modalController
10. **SIEMPRE** pull-to-refresh en paginas con datos (pull-factor 0.5)
11. **SIEMPRE** `<select-student>` como primera fila en paginas de datos
12. **SIEMPRE** `<loading-skeleton>` como fallback de carga
13. **Textos en espanol** — Labels, placeholders, mensajes, todo en espanol
