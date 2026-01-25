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

---

## Estructura de Vistas

### Layout Base

Todas las vistas de lista deben seguir esta estructura:

```vue
<template lang="pug">
  .view-name-container
    // Header con gradiente
    .view-name__header
      // ... contenido del header

    // Contenido principal (ancho completo)
    .view-name__content
      b-card.view-name__card
        // Filtros, tabla, etc.
</template>

<style lang="scss" scoped>
.view-name-container {
  padding: 1.5rem; // IMPORTANTE: padding consistente
}

.view-name__content {
  width: 100%; // IMPORTANTE: ancho completo, NO max-width
}

.view-name__card {
  border: none;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.08);
}
</style>
```

### ⚠️ Reglas Importantes

- ✅ **Padding**: Siempre `1.5rem` en el contenedor principal
- ✅ **Ancho**: Usar `width: 100%`, **NUNCA** `max-width: 1400px`
- ✅ **Cards**: Sin bordes, con box-shadow sutil
- ❌ **NO** centrar contenido con `margin: 0 auto`

---

## Headers con Gradiente

### Estructura HTML (Pug)

```pug
.view-name__header
  .view-name__header-content
    .view-name__header-icon
      b-icon(icon="icon-name" font-scale="2")
    .view-name__header-text
      h2 Título Principal
      p Descripción breve de la vista

  .view-name__header-stats(v-if="!loading")
    .stat-item
      span.stat-value {{ totalCount }}
      span.stat-label Total
```

### Estilos SCSS

```scss
.view-name__header {
  background: linear-gradient(135deg, #color1 0%, #color2 100%);
  border-radius: 16px;
  padding: 2rem;
  margin-bottom: 2rem;
  color: white;
  box-shadow: 0 10px 30px rgba(color-rgb, 0.3);

  // CRÍTICO: Flex para alinear stats a la derecha
  display: flex;
  justify-content: space-between;
  align-items: center;
  flex-wrap: wrap;
  gap: 1rem;
}

.view-name__header-content {
  display: flex;
  align-items: center;
  gap: 1.5rem;
}

.view-name__header-icon {
  width: 64px;
  height: 64px;
  background: rgba(255, 255, 255, 0.2);
  border-radius: 16px;
  display: flex;
  align-items: center;
  justify-content: center;
  backdrop-filter: blur(10px);
}

.view-name__header-text {
  h2 {
    margin: 0;
    font-size: 1.75rem;
    font-weight: 700;
  }

  p {
    margin: 0.25rem 0 0;
    opacity: 0.9;
    font-size: 0.95rem;
  }
}

.view-name__header-stats {
  display: flex;
  gap: 1.5rem;

  .stat-item {
    text-align: center;
    background: rgba(255, 255, 255, 0.15);
    padding: 0.75rem 1.5rem;
    border-radius: 12px;
    backdrop-filter: blur(10px);

    .stat-value {
      display: block;
      font-size: 1.5rem;
      font-weight: 700;
      line-height: 1;
    }

    .stat-label {
      display: block;
      font-size: 0.85rem;
      opacity: 0.9;
      margin-top: 0.25rem;
    }
  }
}
```

---

## Dark Mode Support

**TODAS** las vistas deben tener soporte completo de dark mode. Usar el selector `[data-theme="dark"]`.

### Template Base de Dark Mode

```scss
[data-theme="dark"] {
  // Contenedor principal
  .view-name-container {
    background: #1a202c;
  }

  // Header (solo ajustar shadow)
  .view-name__header {
    box-shadow: 0 10px 30px rgba(color-rgb, 0.2);
  }

  // Cards
  ::v-deep .card {
    background: #2d3748;
    border-color: #4a5568;

    .card-header {
      background: #374151;
      border-color: #4a5568;
      color: #f7fafc;
    }

    .card-body {
      background: #2d3748;
      color: #e2e8f0;
    }
  }

  // Tablas
  ::v-deep .table {
    color: #e2e8f0;

    thead th {
      background: #374151;
      border-color: #4a5568;
      color: #f7fafc;
    }

    tbody {
      background: #2d3748;

      tr {
        background: #2d3748;
        border-color: #4a5568;

        &:hover {
          background: #374151;
        }

        td {
          border-color: #4a5568;
          color: #e2e8f0;
        }
      }
    }
  }

  // Form controls
  ::v-deep .form-control,
  ::v-deep .custom-select,
  ::v-deep select,
  ::v-deep input,
  ::v-deep textarea {
    background: #2d3748;
    border-color: #4a5568;
    color: #e2e8f0;

    &:focus {
      background: #374151;
      border-color: #4f46e5;
      color: #f7fafc;
    }

    option {
      background: #2d3748;
      color: #e2e8f0;
    }
  }

  // Botones
  ::v-deep .btn-primary {
    background: #4f46e5;
    border-color: #4f46e5;

    &:hover, &:focus {
      background: #4338ca;
      border-color: #4338ca;
    }
  }

  ::v-deep .btn-success {
    background: #10b981;
    border-color: #10b981;

    &:hover, &:focus {
      background: #059669;
      border-color: #059669;
    }
  }

  ::v-deep .btn-warning {
    background: #f59e0b;
    border-color: #f59e0b;
    color: #1a202c;

    &:hover, &:focus {
      background: #d97706;
      border-color: #d97706;
    }
  }

  ::v-deep .btn-danger {
    background: #ef4444;
    border-color: #ef4444;

    &:hover, &:focus {
      background: #dc2626;
      border-color: #dc2626;
    }
  }

  ::v-deep .btn-outline-secondary {
    border-color: #4a5568;
    color: #e2e8f0;

    &:hover, &:focus {
      background: #374151;
      border-color: #4a5568;
      color: #f7fafc;
    }
  }

  // Dropdowns
  ::v-deep .dropdown {
    .dropdown-menu {
      background: #2d3748;
      border-color: #4a5568;

      .dropdown-item {
        color: #e2e8f0;

        &:hover, &:focus {
          background: #374151;
          color: #fff;
        }

        &:active {
          background: #4f46e5;
          color: #fff;
        }
      }

      .dropdown-divider {
        border-color: #4a5568;
      }
    }
  }

  // Modales
  ::v-deep .modal {
    .modal-content {
      background: #2d3748;
      border-color: #4a5568;
    }

    .modal-header {
      background: #374151;
      border-color: #4a5568;
      color: #f7fafc;

      .modal-title {
        color: #f7fafc;
      }

      .close {
        color: #f7fafc;
        opacity: 0.8;

        &:hover {
          opacity: 1;
        }
      }
    }

    .modal-body {
      background: #2d3748;
      color: #e2e8f0;
    }

    .modal-footer {
      background: #374151;
      border-color: #4a5568;
    }
  }

  // v-select (vue-select)
  ::v-deep .v-select {
    .vs__dropdown-toggle {
      background: #2d3748;
      border-color: #4a5568;
    }

    .vs__selected {
      color: #e2e8f0;
    }

    .vs__search::placeholder {
      color: #718096;
    }

    .vs__dropdown-menu {
      background: #2d3748;
      border-color: #4a5568;

      .vs__dropdown-option {
        color: #e2e8f0;

        &--highlight {
          background: #4f46e5;
          color: #fff;
        }
      }
    }

    .vs__clear,
    .vs__open-indicator {
      fill: #e2e8f0;
    }
  }

  // Paginación
  ::v-deep .page-item {
    .page-link {
      background: #2d3748;
      border-color: #4a5568;
      color: #e2e8f0;

      &:hover {
        background: #374151;
        border-color: #4a5568;
        color: #fff;
      }
    }

    &.active .page-link {
      background: #4f46e5;
      border-color: #4f46e5;
    }

    &.disabled .page-link {
      background: #1a202c;
      border-color: #4a5568;
      color: #718096;
    }
  }

  // Badges
  ::v-deep .badge {
    &.badge-primary {
      background: #4f46e5;
    }

    &.badge-success {
      background: #10b981;
    }

    &.badge-warning {
      background: #f59e0b;
      color: #1a202c;
    }

    &.badge-danger {
      background: #ef4444;
    }

    &.badge-info {
      background: #06b6d4;
    }
  }

  // Breadcrumbs
  ::v-deep .breadcrumb {
    background: #2d3748;

    .breadcrumb-item {
      color: #a0aec0;

      &.active {
        color: #e2e8f0;
      }

      a {
        color: #60a5fa;

        &:hover {
          color: #93c5fd;
        }
      }
    }
  }

  // Labels y texto
  ::v-deep label {
    color: #e2e8f0;
  }
}
```

### Paleta de Colores Dark Mode

| Uso | Color | Variable |
|-----|-------|----------|
| Background principal | `#1a202c` | Contenedores |
| Background cards | `#2d3748` | Cards, modales |
| Background hover | `#374151` | Hover states |
| Bordes | `#4a5568` | Borders |
| Texto principal | `#e2e8f0` | Títulos, labels |
| Texto secundario | `#a0aec0` | Subtítulos |
| Texto claro | `#f7fafc` | Headers |
| Primary | `#4f46e5` | Botones primary |
| Success | `#10b981` | Botones success |
| Warning | `#f59e0b` | Botones warning |
| Danger | `#ef4444` | Botones danger |

---

## Filtros Colapsables

### Patrón con Acordeón Clickeable

```vue
<template slot="searchInputs">
  <b-col cols="12" class="px-0">
    <b-card class="mb-3 filter-card">
      <template #header>
        <div
          class="filter-card__header"
          @click="filtersVisible = !filtersVisible"
        >
          <h6 class="mb-0">
            <b-icon
              :icon="filtersVisible ? 'chevron-down' : 'chevron-right'"
              class="mr-2"
            ></b-icon>
            Filtros de Búsqueda
          </h6>
        </div>
      </template>

      <b-collapse v-model="filtersVisible">
        <b-row class="no-gutters">
          <!-- Campos de filtro -->
        </b-row>
      </b-collapse>
    </b-card>
  </b-col>
</template>

<script>
export default {
  data() {
    return {
      filtersVisible: true // Abierto por defecto
    }
  }
}
</script>

<style lang="scss" scoped>
.filter-card__header {
  cursor: pointer;
  user-select: none;
  transition: all 0.2s;

  &:hover {
    opacity: 0.8;
  }
}

[data-theme="dark"] {
  .filter-card {
    background: #2d3748;
    border-color: #4a5568;

    ::v-deep .card-header {
      background: #374151;
      border-color: #4a5568;
      color: #f7fafc;
    }

    ::v-deep .card-body {
      background: #2d3748;
    }
  }
}
</style>
```

---

## Icons

The project uses **Bootstrap Vue Icons** as the primary icon system. Font Awesome 4.x icons (`fa fa-*`) are legacy and may not render properly in all components.

### Preferred: Bootstrap Vue Icons

```vue
<!-- Navigation icons -->
<b-icon icon="chevron-left"></b-icon>
<b-icon icon="chevron-right"></b-icon>
<b-icon icon="skip-backward-fill"></b-icon>
<b-icon icon="skip-forward-fill"></b-icon>

<!-- Action icons -->
<b-icon icon="arrow-clockwise"></b-icon>  <!-- Refresh/Sync -->
<b-icon icon="plus"></b-icon>
<b-icon icon="pencil"></b-icon>
<b-icon icon="trash"></b-icon>
<b-icon icon="check"></b-icon>
<b-icon icon="x"></b-icon>

<!-- Status icons -->
<b-icon icon="check-circle"></b-icon>
<b-icon icon="exclamation-circle"></b-icon>
<b-icon icon="info-circle"></b-icon>

<!-- File/Document icons -->
<b-icon icon="file-earmark"></b-icon>
<b-icon icon="download"></b-icon>
<b-icon icon="upload"></b-icon>
```

### Icon in Buttons Pattern

```vue
<!-- With Bootstrap Vue Icons (preferred) -->
<b-button variant="outline-secondary" size="sm" @click="refresh" :disabled="loading">
  <b-spinner v-if="loading" small></b-spinner>
  <b-icon v-else icon="arrow-clockwise"></b-icon>
</b-button>

<!-- With text -->
<b-button variant="primary" @click="save" :disabled="saving">
  <b-spinner v-if="saving" small class="mr-1"></b-spinner>
  <b-icon v-else icon="check" class="mr-1"></b-icon>
  Guardar
</b-button>
```

---

## Botones y Acciones

### Barra de Acciones (tableAction slot)

Los botones de acciones principales van en el slot `tableAction` del componente PageTitle:

```vue
<template slot="tableAction">
  <!-- Botón de edición masiva (si aplica) -->
  <b-button
    v-if="!editMode"
    variant="warning"
    @click="enableEditMode"
    :disabled="!selectedCourse"
    class="mr-2"
    v-b-tooltip.hover
    title="Editar elementos del curso seleccionado"
  >
    <b-icon icon="pencil-square" class="mr-1"></b-icon>
    <span>Editar Masivamente</span>
  </b-button>

  <b-button
    v-if="editMode"
    variant="danger"
    @click="cancelEditMode"
    class="mr-2"
  >
    <b-icon icon="x-circle" class="mr-1"></b-icon>
    <span>Cancelar Edición</span>
  </b-button>

  <!-- Botón de actualizar -->
  <b-button
    variant="outline-primary"
    @click="refresh"
    class="mr-2"
    v-b-tooltip.hover
    title="Actualizar lista"
  >
    <b-spinner v-if="loading" small></b-spinner>
    <b-icon v-else icon="arrow-clockwise"></b-icon>
  </b-button>

  <!-- Dropdown de acciones masivas -->
  <b-dropdown variant="primary" right class="mr-2">
    <template #button-content>
      <b-icon icon="collection" class="mr-1"></b-icon>
      <span>Acciones Masivas</span>
    </template>
    <b-dropdown-item @click="action1">
      <b-icon icon="upload" class="mr-2"></b-icon>
      Acción 1
    </b-dropdown-item>
    <b-dropdown-item @click="action2">
      <b-icon icon="download" class="mr-2"></b-icon>
      Acción 2
    </b-dropdown-item>
  </b-dropdown>
</template>
```

### ⚠️ Reglas de Botones

- ✅ **SIEMPRE** usar Bootstrap Icons (b-icon), **NUNCA** FontAwesome
- ✅ Botones de edición masiva van en `tableAction`, NO dentro de filtros
- ✅ Usar tooltips (`v-b-tooltip.hover`) para botones con solo icono
- ✅ Spinners en botones durante carga
- ❌ NO usar `i.fa` para iconos

---

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

### Gradientes por Vista

| Vista | Gradiente | Colores RGB |
|-------|-----------|-------------|
| Panel UTP | Púrpura | `#667eea → #764ba2` |
| Mi Día | Naranja | `#f59e0b → #d97706` |
| Libro Digital | Verde | `#48bb78 → #38a169` |
| Matrícula | Índigo | `#4f46e5 → #4338ca` |
| Atrasos | Naranja oscuro | `#e07328 → #c45d1e` |
| Agenda | Azul | `#3182ce → #2c5282` |
| Asistencia Diaria | Teal | `#0d9488 → #0f766e` |
| Notas | Azul eléctrico | `#1e40af → #3b82f6` |
| Informe Párvulo | Naranja brillante | `#f97316 → #ea580c` |
| Observaciones | Rojo-Púrpura | `#dc2626 → #b91c1c` |
| Personalidad | Rosa | `#ec4899 → #be185d` |
| Reportes | Púrpura oscuro | `#8b5cf6 → #6d28d9` |

---

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

---

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

---

## Componentes Reutilizables

### PeriodLegend

Componente para mostrar períodos escolares con indicadores visuales.

**Ubicación**: `src/views/attendance/components/PeriodLegend.vue`

**Uso**:
```vue
<PeriodLegend
  :current-establishment-period="currentEstablishmentPeriod"
  :establishment-sub-periods="establishmentSubPeriods"
/>
```

**Props**:
- `currentEstablishmentPeriod` (Object, required): Período escolar actual
- `establishmentSubPeriods` (Array): Array de sub-períodos (semestres, vacaciones)

**Features**:
- Muestra año escolar completo
- Lista de sub-períodos con colores
- Indica período activo con glow
- Dark mode incluido
- Responsive

---

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
10. **Use Bootstrap Vue Icons** (`b-icon`) for new components, Font Awesome only for legacy
11. **Expose `refresh()` methods** on data-loading components for parent sync
12. **Use `ref` attribute** on child components to enable calling their methods

---

## Checklist para Nuevas Vistas

Al crear o actualizar una vista, verificar:

### Estructura
- [ ] Padding `1.5rem` en contenedor principal
- [ ] Ancho completo (`width: 100%`, NO `max-width`)
- [ ] Header con gradiente fuera del card principal
- [ ] Iconos Bootstrap (NO FontAwesome)

### Header
- [ ] Estructura con icon + text + stats
- [ ] Stats alineados a la derecha (`justify-content: space-between`)
- [ ] Gradient con border-radius 16px
- [ ] Box-shadow apropiado
- [ ] Backdrop-filter en icono

### Filtros
- [ ] En acordeón colapsable (clickeable)
- [ ] Icono chevron que cambia dirección
- [ ] Abierto por defecto (`filtersVisible: true`)
- [ ] Dark mode aplicado

### Botones
- [ ] En slot `tableAction` (NO dentro de filtros)
- [ ] Bootstrap Icons en todos los botones
- [ ] Tooltips en botones de solo icono
- [ ] Spinners durante carga

### Tablas
- [ ] Hover habilitado
- [ ] Responsive
- [ ] Sticky header si es necesaria
- [ ] Templates para empty y busy states
- [ ] Dark mode completo

### Dark Mode
- [ ] Contenedor principal
- [ ] Headers (ajustar shadow)
- [ ] Cards (header, body)
- [ ] Tablas (thead, tbody, hover)
- [ ] Form controls (input, select, textarea)
- [ ] Botones (todos los variants)
- [ ] Dropdowns
- [ ] Modales
- [ ] v-select
- [ ] Paginación
- [ ] Badges
- [ ] Breadcrumbs

### Código Limpio
- [ ] Sin código comentado
- [ ] Sin `console.log`
- [ ] Sin imports no usados
- [ ] Sin TODOs sin resolver

---

**Última actualización**: Enero 2026
**Versión**: 3.0 - Incluye dark mode completo, filtros colapsables y estructuras estandarizadas
