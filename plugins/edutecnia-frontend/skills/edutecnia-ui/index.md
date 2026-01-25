---
name: edutecnia-ui
description: Design system and UI patterns for Edutecnia frontend applications. Use when building components, pages or forms for the educational management platform. Implements Vue 2 + Bootstrap-Vue with custom SCSS styling.
---

# Edutecnia UI Design System

This skill provides the design system, component patterns, and styling guidelines for Edutecnia frontend applications. Use it when building interfaces for the educational management platform.

## Technology Stack

- **Framework**: Vue 2.6 + TypeScript
- **UI Library**: Bootstrap-Vue
- **State Management**: Vuex (86 modules)
- **Styling**: SCSS with BEM methodology
- **Icons**: Bootstrap Vue Icons (primary) + Font Awesome 4.x (legacy)

## Icons

The project uses **Bootstrap Vue Icons** as the primary icon system. Font Awesome 4.x icons (`fa fa-*`) are legacy and may not render properly in all components.

### Preferred: Bootstrap Vue Icons

```vue
<!-- Navigation icons -->
<b-icon-chevron-left></b-icon-chevron-left>
<b-icon-chevron-right></b-icon-chevron-right>
<b-icon-skip-backward-fill></b-icon-skip-backward-fill>
<b-icon-skip-forward-fill></b-icon-skip-forward-fill>

<!-- Action icons -->
<b-icon-arrow-clockwise></b-icon-arrow-clockwise>  <!-- Refresh/Sync -->
<b-icon-plus></b-icon-plus>
<b-icon-pencil></b-icon-pencil>
<b-icon-trash></b-icon-trash>
<b-icon-check></b-icon-check>
<b-icon-x></b-icon-x>

<!-- Status icons -->
<b-icon-check-circle></b-icon-check-circle>
<b-icon-exclamation-circle></b-icon-exclamation-circle>
<b-icon-info-circle></b-icon-info-circle>

<!-- File/Document icons -->
<b-icon-file-earmark></b-icon-file-earmark>
<b-icon-download></b-icon-download>
<b-icon-upload></b-icon-upload>
```

### Legacy: Font Awesome 4.x (use sparingly)

```vue
<!-- Only use when Bootstrap Vue Icons don't have equivalent -->
<i class="fa fa-users"></i>
<i class="fa fa-book"></i>
<i class="fa fa-calendar"></i>
<i class="fa fa-clock-o"></i>
<i class="fa fa-graduation-cap"></i>
```

### Icon in Buttons Pattern

```vue
<!-- With Bootstrap Vue Icons (preferred) -->
<b-button variant="outline-secondary" size="sm" @click="refresh" :disabled="loading">
  <b-spinner v-if="loading" small></b-spinner>
  <b-icon-arrow-clockwise v-else></b-icon-arrow-clockwise>
</b-button>

<!-- With text -->
<b-button variant="primary" @click="save" :disabled="saving">
  <b-spinner v-if="saving" small class="mr-1"></b-spinner>
  <b-icon-check v-else class="mr-1"></b-icon-check>
  Guardar
</b-button>
```

## Brand Colors

```scss
// Primary Brand
$edutecnia-orange: #ee7525;
$edutecnia-orange-dark: #d85d0f;
$edutecnia-orange-darker: #c04d00;

// Gradients
$gradient-primary: linear-gradient(135deg, #ee7525 0%, #d85d0f 100%);
$gradient-success: linear-gradient(135deg, #059669 0%, #047857 100%);
$gradient-dark: linear-gradient(90deg, #1e293b 0%, #334155 100%);

// Neutrals (Light Theme)
$slate-50: #f8fafc;
$slate-100: #f1f5f9;
$slate-200: #e2e8f0;
$slate-300: #cbd5e1;
$slate-400: #94a3b8;
$slate-500: #64748b;
$slate-600: #475569;
$slate-700: #334155;
$slate-800: #1e293b;
$slate-900: #0f172a;

// Status Colors
$status-created: #fef3c7;     // Yellow background
$status-created-text: #92400e;
$status-registered: #d1fae5;  // Green background
$status-registered-text: #065f46;
$status-retired: #fee2e2;     // Red background
$status-retired-text: #991b1b;
$status-rejected: #f3e8ff;    // Purple background
$status-rejected-text: #6b21a8;

// Dark Theme
$dark-bg-primary: #1e293b;
$dark-bg-secondary: #0f172a;
$dark-border: #334155;
$dark-text-primary: #f1f5f9;
$dark-text-secondary: #94a3b8;
```

## Typography

```scss
// Font stack (system fonts for performance)
$font-family-base: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;

// Font sizes
$font-size-xs: 11px;
$font-size-sm: 12px;
$font-size-base: 14px;
$font-size-md: 15px;
$font-size-lg: 16px;
$font-size-xl: 18px;
$font-size-2xl: 22px;
$font-size-3xl: 28px;

// Font weights
$font-weight-normal: 400;
$font-weight-medium: 500;
$font-weight-semibold: 600;
$font-weight-bold: 700;

// Labels (form fields)
.label-style {
  font-size: 12px;
  font-weight: 600;
  color: #475569;
  text-transform: uppercase;
  letter-spacing: 0.3px;
  margin-bottom: 6px;
}
```

## SCSS Structure (BEM Methodology)

Use BEM naming convention for all component styles:

```scss
// Component structure
.component-name {
  // Block styles

  // ============================================
  // SECTION NAME
  // ============================================
  &__element {
    // Element styles
  }

  &__element--modifier {
    // Modifier styles
  }

  // Nested elements
  &__parent {
    &-child {
      // Nested child styles
    }
  }
}
```

## Card Components

### Section Card Pattern

```scss
.component__section {
  background: $slate-50;
  border-radius: 10px;
  margin-bottom: 20px;
  border: 1px solid $slate-200;
  overflow: hidden;

  &-header {
    background: $gradient-dark;
    color: white;
    padding: 14px 20px;
    font-size: 14px;
    font-weight: 600;
    text-transform: uppercase;
    letter-spacing: 0.5px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    cursor: pointer;
    transition: all 0.2s ease;

    &:hover {
      background: linear-gradient(90deg, #334155 0%, #475569 100%);
    }

    // Collapsible icon rotation
    i {
      transition: transform 0.3s ease;
    }

    &.collapsed i {
      transform: rotate(-90deg);
    }
  }

  &-body {
    padding: 20px;
    background: white;
  }

  // Color variants
  &--primary &-header {
    background: $gradient-primary;
    &:hover {
      background: linear-gradient(90deg, #d85d0f 0%, #c04d00 100%);
    }
  }

  &--success &-header {
    background: $gradient-success;
  }

  &--info &-header {
    background: linear-gradient(90deg, #0284c7 0%, #0369a1 100%);
  }
}
```

### Info Card Pattern

```scss
.component__info-card {
  display: flex;
  gap: 20px;
  padding: 24px;
  border-radius: 12px;
  margin-top: 16px;

  &--warning {
    background: #fffbeb;
    border: 1px solid #fcd34d;
  }

  &--success {
    background: #ecfdf5;
    border: 1px solid #6ee7b7;
  }

  &--info {
    background: #eff6ff;
    border: 1px solid #93c5fd;
  }

  &__icon {
    width: 48px;
    height: 48px;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    flex-shrink: 0;

    i { font-size: 24px; }
  }

  &__content {
    flex: 1;

    h5 {
      margin: 0 0 8px;
      font-size: 16px;
      font-weight: 600;
      color: $slate-800;
    }

    p {
      margin: 0 0 16px;
      font-size: 14px;
      color: $slate-600;
      line-height: 1.5;
    }
  }
}
```

## Form Controls

### Global Form Styling

```scss
.form-control,
.custom-select,
select.form-control {
  border-radius: 8px !important;
  border: 1.5px solid $slate-200 !important;
  padding: 10px 14px !important;
  font-size: 14px !important;
  height: auto !important;
  min-height: 42px;
  transition: all 0.2s ease;
  background-color: white;
  color: $slate-700;

  &:focus {
    border-color: $edutecnia-orange !important;
    box-shadow: 0 0 0 3px rgba(238, 117, 37, 0.15) !important;
    outline: none;
  }

  &.is-valid {
    border-color: #059669 !important;
    background-image: none;
  }

  &.is-invalid {
    border-color: #dc2626 !important;
    background-image: none;
  }

  &:disabled {
    background-color: $slate-100 !important;
    cursor: not-allowed;
    opacity: 0.7;
  }

  &::placeholder {
    color: $slate-400;
  }
}

// Custom select arrow
.custom-select {
  background-image: url("data:image/svg+xml,%3csvg xmlns='http://www.w3.org/2000/svg' width='12' height='12' fill='%23475569' viewBox='0 0 16 16'%3e%3cpath d='M7.247 11.14 2.451 5.658C1.885 5.013 2.345 4 3.204 4h9.592a1 1 0 0 1 .753 1.659l-4.796 5.48a1 1 0 0 1-1.506 0z'/%3e%3c/svg%3e") !important;
  background-repeat: no-repeat !important;
  background-position: right 12px center !important;
  background-size: 12px !important;
  padding-right: 36px !important;
  appearance: none;
}
```

### Switch Controls

```scss
.custom-switch {
  padding-left: 0;

  .custom-control-input:checked ~ .custom-control-label::before {
    background-color: $edutecnia-orange;
    border-color: $edutecnia-orange;
  }

  .custom-control-label {
    padding-left: 50px;
    font-size: 14px;
    font-weight: 500;
    color: $slate-700;
    cursor: pointer;

    &::before {
      left: 0;
      width: 40px;
      height: 22px;
      border-radius: 11px;
      border: 2px solid $slate-300;
      background-color: $slate-200;
    }

    &::after {
      left: 3px;
      top: calc(0.25rem + 3px);
      width: 16px;
      height: 16px;
      border-radius: 50%;
      background-color: white;
      box-shadow: 0 1px 3px rgba(0,0,0,0.2);
      transition: transform 0.15s ease-in-out;
    }
  }

  .custom-control-input:checked ~ .custom-control-label::after {
    transform: translateX(18px);
  }
}
```

## Buttons

```scss
.btn-edutecnia {
  padding: 10px 24px;
  border-radius: 8px;
  font-weight: 600;
  font-size: 14px;
  display: inline-flex;
  align-items: center;
  gap: 8px;
  transition: all 0.2s ease;
  border: none;
  cursor: pointer;

  &--primary {
    background: $gradient-primary;
    color: white;

    &:hover {
      background: linear-gradient(135deg, #d85d0f 0%, #c04d00 100%);
      transform: translateY(-1px);
      box-shadow: 0 4px 12px rgba(238, 117, 37, 0.3);
    }

    &:disabled {
      opacity: 0.6;
      cursor: not-allowed;
      transform: none;
      box-shadow: none;
    }
  }

  &--secondary {
    background: $slate-100;
    color: $slate-600;
    border: 1px solid $slate-200;

    &:hover {
      background: $slate-200;
    }
  }

  &--success {
    background: $gradient-success;
    color: white;

    &:hover {
      transform: translateY(-1px);
      box-shadow: 0 4px 12px rgba(5, 150, 105, 0.3);
    }
  }

  &--danger {
    background: #fee2e2;
    color: #dc2626;
    border: 1px solid #fecaca;

    &:hover {
      background: #fecaca;
    }
  }
}
```

## Status Badges

```scss
.status-badge {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  padding: 4px 10px;
  border-radius: 20px;
  font-size: 11px;
  font-weight: 600;
  text-transform: uppercase;

  &--created {
    background: $status-created;
    color: $status-created-text;
  }

  &--registered {
    background: $status-registered;
    color: $status-registered-text;
  }

  &--retired {
    background: $status-retired;
    color: $status-retired-text;
  }

  &--rejected {
    background: $status-rejected;
    color: $status-rejected-text;
  }
}
```

## Avatar Component

```scss
.avatar {
  width: 60px;
  height: 60px;
  border-radius: 50%;
  background: $gradient-primary;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  font-weight: 700;
  flex-shrink: 0;

  &--sm {
    width: 40px;
    height: 40px;
    font-size: 14px;
  }

  &--lg {
    width: 80px;
    height: 80px;
    font-size: 28px;
  }

  img {
    width: 100%;
    height: 100%;
    border-radius: 50%;
    object-fit: cover;
  }
}
```

## Loading States

```scss
.loading-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(255, 255, 255, 0.9);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  z-index: 1000;

  &__spinner {
    width: 48px;
    height: 48px;
    border: 4px solid $slate-200;
    border-top-color: $edutecnia-orange;
    border-radius: 50%;
    animation: spin 1s linear infinite;
  }

  &__text {
    margin-top: 16px;
    color: $slate-600;
    font-size: 14px;
    font-weight: 500;
  }

  @keyframes spin {
    to { transform: rotate(360deg); }
  }
}
```

## Tab Navigation

```scss
.tabs-container {
  background: white;
  border-radius: 12px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  overflow: hidden;

  .nav-tabs {
    border-bottom: 2px solid $slate-200;
    padding: 0 16px;
    background: $slate-50;
    flex-wrap: nowrap;
    overflow-x: auto;

    .nav-item {
      margin-bottom: -2px;
    }

    .nav-link {
      border: none;
      border-bottom: 3px solid transparent;
      padding: 16px 20px;
      color: $slate-500;
      font-weight: 500;
      font-size: 14px;
      white-space: nowrap;
      transition: all 0.2s ease;
      display: flex;
      align-items: center;
      gap: 8px;

      i { font-size: 16px; }

      &:hover {
        color: $edutecnia-orange;
        background: transparent;
      }

      &.active {
        color: $edutecnia-orange;
        border-bottom-color: $edutecnia-orange;
        background: white;
      }
    }
  }

  .tab-content {
    padding: 24px;
  }
}
```

## Dark Mode Support

Always include dark mode styles for new components:

```scss
[data-theme='dark'] {
  .component-name {
    background: $dark-bg-primary;

    &__element {
      background: $dark-bg-secondary;
      border-color: $dark-border;
      color: $dark-text-primary;
    }

    // Form controls in dark mode
    .form-control,
    .custom-select {
      background: $dark-bg-secondary !important;
      border-color: $dark-border !important;
      color: $dark-text-primary !important;

      &::placeholder {
        color: $slate-500;
      }
    }

    // Info cards in dark mode
    &__info-card {
      &--warning {
        background: #3d2f14;
        border-color: #92400e;
      }

      &--success {
        background: #14352a;
        border-color: #047857;
      }

      &--info {
        background: #1e3a5f;
        border-color: #1d4ed8;
      }
    }

    // Text colors in dark mode
    h1, h2, h3, h4, h5, h6 {
      color: $dark-text-primary;
    }

    p, span, label {
      color: $dark-text-secondary;
    }
  }
}
```

## Responsive Breakpoints

```scss
// Breakpoints
$breakpoint-sm: 576px;
$breakpoint-md: 768px;
$breakpoint-lg: 992px;
$breakpoint-xl: 1200px;

// Usage
@media (max-width: $breakpoint-lg) {
  // Tablet styles
}

@media (max-width: $breakpoint-md) {
  // Mobile styles
}

// Grid layout example
.field-row {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 20px;

  @media (max-width: $breakpoint-lg) {
    grid-template-columns: repeat(2, 1fr);
  }

  @media (max-width: $breakpoint-sm) {
    grid-template-columns: 1fr;
  }
}
```

## Vue Component Patterns

### Form Component Structure

```vue
<template>
  <div class="component-name">
    <!-- Loading Overlay -->
    <div v-if="loading" class="component-name__loading">
      <div class="component-name__loading-spinner"></div>
      <div class="component-name__loading-text">Cargando...</div>
    </div>

    <!-- Header -->
    <div class="component-name__header">
      <div class="component-name__header-info">
        <div class="component-name__header-avatar">{{ initials }}</div>
        <div class="component-name__header-details">
          <h2>{{ title }}</h2>
          <p>{{ subtitle }}</p>
        </div>
      </div>
      <div class="component-name__header-actions">
        <button class="btn-edutecnia btn-edutecnia--secondary" @click="back">
          <i class="fa fa-arrow-left"></i>
          Volver
        </button>
      </div>
    </div>

    <!-- Tabs -->
    <div class="component-name__tabs">
      <b-tabs v-model="activeTab">
        <b-tab>
          <template #title>
            <i class="fa fa-user mr-2"></i>
            Tab 1
          </template>
          <!-- Tab content -->
        </b-tab>
      </b-tabs>
    </div>

    <!-- Actions Bar -->
    <div class="component-name__actions">
      <div class="component-name__actions-left">
        <i class="fa fa-info-circle"></i>
        <span>Información adicional</span>
      </div>
      <div class="component-name__actions-right">
        <button class="btn-edutecnia btn-edutecnia--secondary" @click="cancel">
          Cancelar
        </button>
        <button class="btn-edutecnia btn-edutecnia--primary" @click="save" :disabled="loading">
          <b-spinner v-if="loading" small></b-spinner>
          <i v-else class="fa fa-check"></i>
          Guardar
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import { mapActions, mapGetters } from 'vuex'

export default {
  name: 'ComponentName',

  data() {
    return {
      activeTab: 0,
      loading: false,
      // form fields...
    }
  },

  computed: {
    ...mapGetters('module', ['getData']),
  },

  async mounted() {
    await this.init()
  },

  methods: {
    ...mapActions('module', ['fetchData', 'saveData']),

    async init() {
      this.loading = true
      try {
        await this.fetchData()
      } catch (error) {
        this.$toast.error('Error al cargar los datos')
      } finally {
        this.loading = false
      }
    },

    async save() {
      // Validation and save logic
    },

    back() {
      this.$router.go(-1)
    }
  }
}
</script>
```

## Modal with Custom Header

Professional modal design with gradient header and status indicators:

```vue
<template lang="pug">
  b-modal(
    ref="modal"
    centered
    hide-footer
    size="xl"
    modal-class="custom-modal"
    header-class="custom-modal__header-wrapper"
    body-class="custom-modal__body"
    hide-header
  )
    //- Custom Header
    .modal__header
      .modal-header__main
        .modal-header__icon
          i.fa.fa-users
        .modal-header__info
          h3.modal-header__title {{ title }}
          .modal-header__meta
            span.meta-item
              i.fa.fa-calendar
              | {{ date }}
            span.meta-item
              i.fa.fa-clock-o
              | {{ time }}

      //- Status Indicators
      .modal-header__status
        .status-pill(:class="{ active: isComplete }")
          i.fa.fa-check-circle
          span {{ isComplete ? 'Completado' : 'Pendiente' }}
        .status-pill(:class="{ active: isConfirmed }")
          i.fa.fa-lock
          span {{ isConfirmed ? 'Confirmado' : 'Sin confirmar' }}

      button.modal-header__close(type="button" @click="hide" aria-label="Cerrar")
        span(aria-hidden="true") &times;
</template>
```

```scss
.modal__header {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  width: 100%;
  padding: 20px 24px;
  background: linear-gradient(135deg, #3b82f6 0%, #1d4ed8 100%);
  color: white;
  border-radius: 8px 8px 0 0;
}

.modal-header__main {
  display: flex;
  align-items: flex-start;
  gap: 16px;
  flex: 1;
}

.modal-header__icon {
  width: 48px;
  height: 48px;
  background: rgba(255, 255, 255, 0.2);
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 22px;
}

.modal-header__title {
  margin: 0 0 8px 0;
  font-size: 20px;
  font-weight: 600;
}

.modal-header__meta {
  display: flex;
  gap: 16px;
  flex-wrap: wrap;
}

.meta-item {
  display: flex;
  align-items: center;
  gap: 6px;
  font-size: 13px;
  opacity: 0.9;

  i { font-size: 12px; }
}

.modal-header__status {
  display: flex;
  gap: 8px;
  flex-wrap: wrap;
}

.status-pill {
  display: flex;
  align-items: center;
  gap: 6px;
  padding: 6px 12px;
  background: rgba(255, 255, 255, 0.15);
  border-radius: 20px;
  font-size: 11px;
  font-weight: 500;
  white-space: nowrap;

  &.active {
    background: rgba(16, 185, 129, 0.9);
  }

  i { font-size: 12px; }
}

.modal-header__close {
  background: rgba(255, 255, 255, 0.2);
  border: none;
  color: white;
  width: 36px;
  height: 36px;
  border-radius: 8px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 24px;
  margin-left: 16px;
  transition: background 0.15s;

  &:hover {
    background: rgba(255, 255, 255, 0.3);
  }
}
```

## Calendar Component Pattern

Monthly calendar grid with status indicators:

```scss
.calendar-container {
  background: #fff;
  border-radius: 12px;
  padding: 20px;
  box-shadow: 0 1px 3px rgba(0,0,0,0.08);
}

.calendar-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 16px;
}

.calendar-legend {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
  padding: 10px 12px;
  background: #f9fafb;
  border-radius: 8px;
  margin-bottom: 12px;
}

.legend-item {
  display: flex;
  align-items: center;
  gap: 6px;
  font-size: 11px;
  color: #6b7280;
}

.dot {
  width: 10px;
  height: 10px;
  border-radius: 50%;

  &.complete { background: #10b981; }
  &.partial { background: #f59e0b; }
  &.pending { background: #ef4444; }
  &.future { background: #93c5fd; }
  &.weekend { background: #d1d5db; }
}

.month-grid {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  gap: 4px;
}

.day-cell {
  min-height: 60px;
  padding: 6px;
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  background: #fff;
  border: 1px solid #e9ecef;
  transition: all 0.15s;

  &:hover:not(.empty) {
    border-color: #3b82f6;
  }

  &.today { border: 2px solid #f97316; background: #fff7ed; }
  &.selected { border: 2px solid #3b82f6; background: #eff6ff; }
  &.complete { background: #ecfdf5; border-color: #6ee7b7; }
  &.partial { background: #fffbeb; border-color: #fcd34d; }
  &.pending { background: #fef2f2; border-color: #fca5a5; }
}

.day-number {
  font-weight: 600;
  font-size: 13px;
  color: #374151;
}

.day-courses-count {
  font-size: 10px;
  font-weight: 600;
  color: #6b7280;
  background: #f3f4f6;
  padding: 2px 4px;
  border-radius: 4px;
}
```

## Pending Items Panel

Sidebar panel showing pending items as clickable chips:

```scss
.pending-panel {
  background: #fff;
  border-radius: 12px;
  padding: 16px;
  box-shadow: 0 1px 3px rgba(0,0,0,0.08);
  border-left: 4px solid #ef4444;
}

.pending-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  margin-bottom: 12px;
}

.pending-title {
  margin: 0;
  font-size: 14px;
  font-weight: 600;
  color: #991b1b;
  display: flex;
  align-items: center;
  gap: 8px;
}

.pending-list {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
}

.pending-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 8px 12px;
  background: #fef2f2;
  border: 1px solid #fecaca;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.15s;

  &:hover {
    background: #fee2e2;
    border-color: #fca5a5;
  }
}

.pending-date {
  font-weight: 600;
  font-size: 12px;
  color: #991b1b;
}

.pending-count {
  font-size: 10px;
  color: #dc2626;
}
```

## Refresh/Sync Pattern

Components that load data should expose a public `refresh()` method for parent components to call:

```javascript
export default {
  methods: {
    // Public method - can be called via $refs from parent
    async refresh() {
      await Promise.all([
        this.loadData(),
        this.loadRelatedData()
      ]);
    },

    async loadData() {
      this.loading = true;
      try {
        // Load data...
      } finally {
        this.loading = false;
      }
    }
  }
}

// Parent component usage:
// this.$refs.childComponent.refresh();
```

## Course/Item Card Grid

Grid layout for displaying items with status indicators:

```scss
.items-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 12px;

  @media (max-width: 1400px) {
    grid-template-columns: repeat(2, 1fr);
  }

  @media (max-width: 768px) {
    grid-template-columns: 1fr;
  }
}

.item-card {
  padding: 12px;
  border-radius: 8px;
  border: 1px solid #e5e7eb;
  border-left: 4px solid #e5e7eb;
  background: #fff;
  transition: all 0.2s;

  &:hover {
    box-shadow: 0 2px 8px rgba(0,0,0,0.08);
  }

  &.confirmed {
    border-left-color: #10b981;
    background: #ecfdf5;
  }

  &.passed {
    border-left-color: #f59e0b;
    background: #fffbeb;
  }

  &.pending {
    border-left-color: #ef4444;
    background: #fef2f2;
  }

  &.selected {
    border-color: #3b82f6;
    box-shadow: 0 0 0 2px rgba(59, 130, 246, 0.2);
  }
}

.item-card-header {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-bottom: 8px;
}

.item-name {
  flex: 1;
  font-weight: 600;
  color: #1f2937;
}

.item-card-actions {
  display: flex;
  gap: 6px;
  padding-top: 8px;
  border-top: 1px solid #e5e7eb;
}
```

## Best Practices

1. **Always use BEM naming** for SCSS classes
2. **Include dark mode styles** for every new component
3. **Use CSS variables** for colors that change between themes
4. **Use `!important`** sparingly, only to override Bootstrap defaults
5. **Test responsiveness** at all breakpoints
6. **Use transitions** for hover states and state changes (0.2s ease)
7. **Prefer gradients** over flat colors for primary buttons and headers
8. **Use box-shadow** for elevation and depth
9. **Include loading states** for async operations
10. **Use Bootstrap Vue Icons** (`b-icon-*`) for new components, Font Awesome only for legacy
11. **Expose `refresh()` methods** on data-loading components for parent sync
12. **Use `ref` attribute** on child components to enable calling their methods

## File Organization

```
src/styles/
├── abstracts/
│   └── _variables.scss      # Global variables
├── base/
│   └── _typography.scss     # Base typography
├── components/
│   └── _table.scss          # Shared components
├── pages/
│   └── _component_name.scss # Page-specific styles
└── main.scss                # Import all partials
```

When creating a new page component, create its SCSS file in `pages/` and import it in `main.scss`.
