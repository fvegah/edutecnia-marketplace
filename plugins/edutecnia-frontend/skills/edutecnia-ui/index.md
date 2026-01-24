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
- **Icons**: Font Awesome 4.x

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
10. **Use Font Awesome 4.x** icons with `fa` prefix

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
