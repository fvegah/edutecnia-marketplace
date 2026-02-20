---
name: mobile-ui-standard
description: Standard UI patterns for Edutecnia mobile apps (Ionic Vue + Capacitor). Covers design tokens, responsive design, dark mode, page structure, accessibility, modals, forms, components, icons, and styling for parents, teachers, and students apps.
---

# Edutecnia UI Mobile Standard (Ionic Vue + Capacitor)

> **Alcance:** Solo aplica a proyectos del ecosistema Edutecnia. No usar en otros proyectos.

## Descripcion
Estandar de UI para las apps moviles de Edutecnia. Define patrones, componentes y convenciones para construir interfaces consistentes usando Ionic Vue 8 + Vue 3 + Capacitor 7 + Vite. Aplica a: edutecnia-parents-app, edutecnia-teachers-app, edutecnia-students-app, y cualquier proyecto Ionic Vue nuevo.

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

## Design Tokens

Definidos en `src/theme/base.css` dentro de `:root`. Usar SIEMPRE estas variables en lugar de valores hardcodeados:

### Tipografia
```css
:root {
  --edu-font-size-xs: 0.75rem;   /* 12px */
  --edu-font-size-sm: 0.8125rem; /* 13px */
  --edu-font-size-base: 0.875rem; /* 14px */
  --edu-font-size-md: 1rem;       /* 16px */
  --edu-font-size-lg: 1.125rem;   /* 18px */
  --edu-font-size-xl: 1.5rem;     /* 24px */
  --edu-font-size-2xl: 1.75rem;   /* 28px */
}
```

### Espaciado
```css
:root {
  --edu-space-xs: 4px;
  --edu-space-sm: 8px;
  --edu-space-md: 12px;
  --edu-space-base: 16px;
  --edu-space-lg: 24px;
  --edu-space-xl: 32px;
  --edu-space-2xl: 48px;
}
```

**Uso correcto:**
```css
/* SI */
.page-header { margin-bottom: var(--edu-space-lg); }
.page-header h1 { font-size: var(--edu-font-size-2xl); }

/* NO */
.page-header { margin-bottom: 24px; }
.page-header h1 { font-size: 1.75rem; }
```

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
├── theme/                   # CSS: base.css (incluye dark mode), auth/login.css
├── utils/                   # Helpers (theme.js, etc.)
├── views/                   # Paginas principales
│   └── session/             # Login, Recover, ChangePassword
├── main.ts                  # Entry point
└── App.vue                  # Root <ion-app>
```

## Responsive Design

### Breakpoints estandar
```
480px   → Small phones (iPhone SE): padding reducido
768px   → Tablet: grids 2 columnas, containers mas angostos
992px   → Split pane activo (ion-split-pane when="lg")
1024px  → Desktop: grids 3+ columnas, auto-fit layouts
```

### Responsive Containers

Toda pagina debe envolver su contenido en un container responsivo. Definidos globalmente en `base.css`:

```css
.responsive-container {
    max-width: 900px;
    margin: 0 auto;
    width: 100%;
}

.responsive-container--wide {
    max-width: 1100px;  /* Para paginas con grids (horario, evaluaciones) */
}

.responsive-container--narrow {
    max-width: 600px;   /* Para perfil, configuracion */
}
```

**Uso en template:**
```html
<ion-content :fullscreen="true" class="ion-padding">
    <div class="responsive-container">
        <!-- contenido -->
    </div>
</ion-content>
```

### Small phones (480px)
```css
@media (max-width: 480px) {
    .responsive-container { padding-left: 0; padding-right: 0; }
    ion-content.ion-padding {
        --padding-start: 12px;
        --padding-end: 12px;
    }
}
```

### Tablet (768-991px)
```css
@media (min-width: 768px) and (max-width: 991px) {
    .responsive-container { max-width: 720px; }
}
```

### Grid responsive en paginas de listas
```css
/* 768px: 2 columnas */
@media (min-width: 768px) {
    .classmates-list { display: grid; grid-template-columns: repeat(2, 1fr); gap: 0 16px; }
}

/* 1024px: 3 columnas */
@media (min-width: 1024px) {
    .classmates-list { grid-template-columns: repeat(3, 1fr); }
}
```

## Estilos Globales Compartidos (DRY)

Estos estilos estan en `base.css` y son globales. **NO duplicarlos en estilos scoped de cada pagina:**

### Page Header
```css
.page-header {
    margin-bottom: var(--edu-space-lg);
}
.page-header h1 {
    margin: 0 0 var(--edu-space-xs) 0;
    font-size: var(--edu-font-size-2xl);
    font-weight: 700;
    line-height: 1.2;
}
.page-header p {
    margin: 0;
    color: var(--ion-color-medium);
    line-height: 1.4;
}
```

### Section Header
```css
.section-header h2 {
    font-size: var(--edu-font-size-base);
    font-weight: 600;
    color: var(--ion-color-medium);
    text-transform: uppercase;
    letter-spacing: 0.5px;
}
```

### Empty State
```css
.empty-state {
    text-align: center;
    padding: var(--edu-space-2xl) var(--edu-space-lg);
}
.empty-state .empty-icon {
    font-size: 64px;
    color: var(--ion-color-medium);
    margin-bottom: var(--edu-space-base);
}
.empty-state h3 { margin: 0 0 var(--edu-space-sm) 0; }
.empty-state p { margin: 0; color: var(--ion-color-medium); }
```

### Loading Container
```css
.loading-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: var(--edu-space-2xl);
}
.loading-container p {
    margin-top: var(--edu-space-base);
    color: var(--ion-color-medium);
}
.loading-container.small { padding: var(--edu-space-lg); }
```

## Accesibilidad (ARIA)

### Botones de refresh
Todos los botones de accion en headers deben tener `aria-label`:
```html
<ion-button @click="loadData" :disabled="loading" aria-label="Actualizar horario">
    <ion-icon slot="icon-only" :icon="refreshOutline"></ion-icon>
</ion-button>
```

### Menu de navegacion
```html
<nav class="menu-nav" aria-label="Navegacion principal">
    <router-link
        v-for="item in appPages"
        :to="item.url"
        :aria-current="isActive(item.url) ? 'page' : undefined"
    >
        <ion-icon :icon="item.iosIcon" aria-hidden="true"></ion-icon>
        <span>{{ item.title }}</span>
    </router-link>
</nav>
```

**Reglas ARIA:**
- `aria-label` en botones icon-only (sin texto visible)
- `aria-current="page"` en el item activo del menu
- `aria-hidden="true"` en iconos decorativos (que acompanan texto)
- `aria-label` en secciones interactivas del menu (ej: "Ver mi perfil" en el card de usuario)

### Focus indicators
```css
:focus-visible {
    outline: 2px solid var(--ion-color-primary);
    outline-offset: 2px;
}
ion-button:focus-visible,
ion-item:focus-visible,
.menu-item:focus-visible {
    outline: 2px solid var(--ion-color-primary);
    outline-offset: 2px;
}
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
  <ion-split-pane content-id="main-content" when="lg">
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

- Split pane con `when="lg"` (se abre permanentemente a 992px+)
- Side menu `type="overlay"` para mobile
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
- Titulo: `"Edutecnia | NombreApp"` (ej: "Edutecnia | Apoderados", "Edutecnia | Docentes", "Edutecnia | Estudiantes")

## AppMenu (Side Drawer)

Logo en la parte superior, user card con link a perfil, dos secciones de items (navegacion principal + configuracion).

```html
<div class="app-menu">
    <!-- Logo -->
    <div class="menu-header">
        <EduLogo size="medium" />
    </div>

    <!-- User Card (link a perfil) -->
    <router-link to="/app/perfil" class="user-section" aria-label="Ver mi perfil">
        <div class="user-avatar">
            <ion-icon :icon="personCircleOutline" aria-hidden="true"></ion-icon>
        </div>
        <div class="user-info">
            <span class="user-name">{{ userData.name }}</span>
            <span class="user-role">Estudiante</span>
        </div>
    </router-link>

    <!-- Nav principal -->
    <nav class="menu-nav" aria-label="Navegacion principal">
        <router-link v-for="item in appPages" :key="item.id" :to="item.url"
            class="menu-item" :class="{ active: isActive(item.url) }"
            :aria-current="isActive(item.url) ? 'page' : undefined">
            <ion-icon :icon="item.iosIcon" aria-hidden="true"></ion-icon>
            <span>{{ item.title }}</span>
        </router-link>
    </nav>

    <!-- Footer (configuracion) -->
    <div class="menu-footer">
        <router-link v-for="item in otherPages" :key="item.id" :to="item.url" class="menu-item">
            <ion-icon :icon="item.iosIcon"></ion-icon>
            <span>{{ item.title }}</span>
        </router-link>
    </div>
</div>
```

**Importante:** NO duplicar "Mi Perfil" en `otherPages` si el user card ya linkea a perfil.

```javascript
const appPages = [
  { id: 1, title: 'Inicio',         url: '/app/inicio',         iosIcon: homeOutline },
  { id: 2, title: 'Agenda',         url: '/app/agenda',         iosIcon: bookOutline },
  { id: 3, title: 'Horario',        url: '/app/horario',        iosIcon: calendarOutline },
  { id: 4, title: 'Calificaciones', url: '/app/calificaciones', iosIcon: nutritionOutline },
  // ...
];
const otherPages = [
  { id: 9, title: 'Configuraciones', url: '/app/configuraciones', iosIcon: settingsOutline },
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

/* iOS selected state */
ion-menu.ios ion-item.selected ion-icon { color: var(--ion-color-primary); }
ion-menu.ios ion-item ion-icon { font-size: 24px; color: #73849a; }

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
    <ion-header>
      <ion-toolbar>
        <ion-buttons slot="start">
          <ion-menu-button color="primary"></ion-menu-button>
        </ion-buttons>
        <ion-title>Nombre Pagina</ion-title>
        <ion-buttons slot="end">
          <ion-button @click="loadData" :disabled="loading" aria-label="Actualizar nombre">
            <ion-icon slot="icon-only" :icon="refreshOutline"></ion-icon>
          </ion-button>
        </ion-buttons>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true" class="ion-padding">
      <!-- Pull-to-Refresh -->
      <ion-refresher slot="fixed" @ionRefresh="handleRefresh($event)"
        :pull-factor="0.5" :pull-min="100" :pull-max="200">
        <ion-refresher-content></ion-refresher-content>
      </ion-refresher>

      <div class="responsive-container">
        <div class="page-header">
          <h1>Nombre Pagina</h1>
          <p>Subtitulo descriptivo</p>
        </div>

        <!-- Loading -->
        <loading-skeleton v-if="loading" variant="cards" :count="4"/>

        <!-- Error -->
        <ion-card v-else-if="error" color="danger">
          <ion-card-content>
            <p>{{ error }}</p>
            <ion-button fill="clear" @click="loadData">Reintentar</ion-button>
          </ion-card-content>
        </ion-card>

        <!-- Content -->
        <div v-else>
          <!-- contenido de la pagina -->
        </div>
      </div>
    </ion-content>
  </ion-page>
</template>
```

Convenciones:
- `ion-content :fullscreen="true"` con `class="ion-padding"`
- Header con `ion-menu-button` + titulo + boton refresh con `aria-label`
- Todo envuelto en `<div class="responsive-container">` (o `--wide`/`--narrow`)
- `ion-refresher` con `slot="fixed"`, `:pull-factor="0.5"`, `:pull-min="100"`, `:pull-max="200"`
- `.page-header` con h1 y p (estilos globales, NO duplicar en scoped)
- `<loading-skeleton>` con variant adecuado como fallback de carga
- Error card con `color="danger"` y boton de reintentar
- `v-if="loading"` / `v-else-if="error"` / `v-else` para estados

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
  loadData().finally(() => {
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

Selector tipo acordeon que muestra el estudiante activo y permite cambiar entre hijos. Solo aplica a parents-app.

```html
<div class="student-selector">
  <ion-item class="selected-student-bar" button @click="isAccordionOpen = !isAccordionOpen">
    <ion-avatar slot="start">...</ion-avatar>
    <ion-label>
      <h2>{{ name }}</h2>
      <p>{{ course_name }}</p>
      <p class="establishment-info">{{ establishment.name }}</p>
    </ion-label>
  </ion-item>
  <div class="students-accordion" :class="{ 'open': isAccordionOpen }">
    <ion-list>
      <ion-item v-for="student in otherStudents" ...></ion-item>
    </ion-list>
  </div>
</div>
```

Emite: `@on-select-student`

```css
.selected-student-bar { --background: var(--ion-color-light); border-radius: 8px; margin: 0.5rem; }
.students-accordion { max-height: 0; overflow: hidden; transition: max-height 0.3s ease-out; }
.students-accordion.open { max-height: 300px; overflow-y: auto; box-shadow: 0 2px 8px rgba(0,0,0,0.1); }
```

## Componente: LoadingSkeleton

Componente adaptable con 3 variantes y cantidad configurable. Usar la variante que mejor represente el contenido que se esta cargando.

```html
<!-- src/components/common/loadingSkeleton.vue -->
<script setup>
defineProps({
    variant: {
        type: String,
        default: 'list',
        validator: (v) => ['list', 'grid', 'cards'].includes(v)
    },
    count: {
        type: Number,
        default: 3
    }
});
</script>
```

### Variantes

| Variante | Uso | Grid responsive |
|----------|-----|-----------------|
| `list` (default) | Listas con thumbnail (companeros, items) | No |
| `grid` | Dashboard quick-access grid | 2→3→4 cols (480→768→1024px) |
| `cards` | Paginas con tarjetas (agenda, notas, horario) | 1→2 cols (768px+) |

### Uso por pagina
```html
<!-- Companeros (lista) -->
<loading-skeleton v-if="loading" :count="6"/>

<!-- Dashboard (grid de accesos rapidos) -->
<loading-skeleton v-if="loading" variant="grid" :count="6"/>

<!-- Agenda, Notas, Asistencia (cards) -->
<loading-skeleton v-if="loading" variant="cards" :count="4"/>

<!-- Horario (cards con mas items) -->
<loading-skeleton v-if="loading" variant="cards" :count="5"/>
```

### Estructura interna de cada variante

**list:** `ion-list` con header + N items con thumbnail + 3 lineas (80%, 60%, 30%)

**grid:** CSS grid (2 cols default) con cards centrados (icono 48px + 2 lineas texto)

**cards:** Flex vertical (grid 2 cols en 768px+) con cards que tienen icono 50px + 3 lineas de contenido

### Spinner inline (para cargas secundarias dentro de pagina)
```html
<div class="loading-container small">
    <ion-spinner name="crescent" color="primary"></ion-spinner>
</div>
```

Spinner variant: siempre `name="crescent"`.

## Patron: Visibilidad condicional sin !important

**NUNCA usar `display: block !important`** para sobreescribir `v-show` en grids responsivos. En su lugar, usar clases CSS:

```html
<!-- INCORRECTO (anti-pattern) -->
<div v-show="selectedDay === day.day" class="day-content">...</div>
<!-- Requiere !important para sobreescribir v-show en desktop -->

<!-- CORRECTO -->
<div :class="['day-content', { 'day-content--active': selectedDay === day.day }]">...</div>
```

```css
/* Mobile: solo mostrar el dia activo */
.day-content { display: none; }
.day-content--active { display: block; }

/* Tablet: 2 columnas, todos visibles */
@media (min-width: 768px) {
    .day-schedule { display: grid; grid-template-columns: repeat(2, 1fr); gap: 16px; }
    .day-content { display: block; }
    .day-tabs { display: none; } /* Ocultar selector de tabs */
}

/* Desktop: auto-fit */
@media (min-width: 1024px) {
    .day-schedule { grid-template-columns: repeat(auto-fit, minmax(180px, 1fr)); }
}
```

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
<ion-modal :is-open="showModal" @did-dismiss="closeModal">
  <ion-header>
    <ion-toolbar>
      <ion-title>Detalle</ion-title>
      <ion-buttons slot="end">
        <ion-button @click="closeModal">
          <ion-icon :icon="closeOutline"></ion-icon>
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

/* Small phones */
@media (max-width: 480px) {
    .login-card { margin: 8px; max-width: none; width: calc(100% - 16px); }
}
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
  sunny, moon, refreshOutline,
} from 'ionicons/icons';
```

Renderizar con variantes de plataforma:
```html
<ion-icon :ios="homeOutline" :md="homeSharp"></ion-icon>
```

Para iconos decorativos (acompanan texto), agregar `aria-hidden="true"`:
```html
<ion-icon :icon="item.iosIcon" aria-hidden="true"></ion-icon>
```

## Tipografia y Espaciado

### Jerarquia de tamanos (usar design tokens)
```
var(--edu-font-size-2xl)  → Titulo de pagina (h1)
var(--edu-font-size-xl)   → Titulo de seccion principal
var(--edu-font-size-lg)   → Titulo de card, subtitulos
var(--edu-font-size-md)   → Texto principal, contenido
var(--edu-font-size-base) → Texto secundario, labels
var(--edu-font-size-sm)   → Texto small, descriptions
var(--edu-font-size-xs)   → Establishment info, meta texto
```

### Border radius
```
4px  → Menu items (MD), badge de entorno
8px  → Cards, listas, student bar, botones, login card
12px → Skeleton cards, subject badges, search bars
50%  → Avatares, percentage circles
```

## Avatar Sizes

```
100px × 100px  → Perfil
80px × 80px    → Detalle de companero
48px × 48px    → Subject badges en evaluaciones
44px × 44px    → Lista de companeros
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
  /app/evaluaciones             → EvaluationsPage
  /app/evaluaciones/realizar/:id → TakeEvaluationPage
  /app/observaciones            → NotesPage
  /app/asistencia               → AttendancePage
  /app/companeros               → ClassmatesPage
  /app/perfil                   → ProfilePage
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
| `480px` | Breakpoint small phones |
| `768px` | Breakpoint tablet (grid 2 cols) |
| `992px` | Breakpoint split-pane (`when="lg"`) |
| `1024px` | Breakpoint desktop (grid 3+ cols) |
| `8px` | Border-radius estandar |
| `12px` | Border-radius cards/search |
| `crescent` | Variante de spinner |
| `0.3s ease-out` | Animacion del acordeon |
| `300px` | Max-height del acordeon abierto |
| `900px` | Max-width responsive-container |
| `1100px` | Max-width responsive-container--wide |
| `600px` | Max-width responsive-container--narrow |
| `pull-factor 0.5, min 100, max 200` | Refresher config |
| `2000-3000ms` | Duracion de toasts |

## Reglas Estrictas

1. **NO usar Vuetify** — Solo componentes Ionic
2. **NO usar SCSS en archivos nuevos** — Solo CSS plano con variables Ionic + design tokens
3. **NO usar Tailwind** — Solo utilidades CSS de Ionic y CSS custom
4. **NO usar Options API** — Solo Composition API con `<script setup>`
5. **NO usar Pinia/Vuex** — Usar `ref()` + Capacitor Preferences
6. **NO usar Lucide** — Solo ionicons (variantes ios/md)
7. **NO duplicar estilos globales** — `.page-header`, `.empty-state`, `.loading-container`, `.section-header` estan en `base.css`
8. **NO usar `display: block !important`** — Usar clases CSS para visibilidad condicional
9. **NO hardcodear tamaños/espacios** — Usar design tokens (`--edu-font-size-*`, `--edu-space-*`)
10. **SIEMPRE** usar componentes Ionic (ion-button, ion-input, ion-list, etc.)
11. **SIEMPRE** `label-placement="stacked"` en inputs
12. **SIEMPRE** `initialBreakpoint: 0.85` en modales con modalController
13. **SIEMPRE** pull-to-refresh en paginas con datos (pull-factor 0.5)
14. **SIEMPRE** envolver contenido en `<div class="responsive-container">`
15. **SIEMPRE** `<loading-skeleton>` con variant adecuado como fallback de carga
16. **SIEMPRE** `aria-label` en botones icon-only
17. **SIEMPRE** `aria-hidden="true"` en iconos decorativos
18. **Textos en espanol** — Labels, placeholders, mensajes, todo en espanol
