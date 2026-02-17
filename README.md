# Edutecnia Marketplace

Marketplace de plugins Claude Code para el ecosistema Edutecnia. Contiene skills de diseño UI, patrones de modelos y guías de desarrollo para aplicaciones educativas.

## Estructura

```
edutecnia-marketplace/
├── .claude-plugin/
│   └── marketplace.json     # Configuración del marketplace
├── plugins/
│   ├── edutecnia-frontend/  # Sistema de diseño UI
│   │   ├── plugin.json
│   │   └── skills/
│   │       ├── edutecnia-ui/
│   │       │   └── index.md
│   │       ├── nuxt4-admin/
│   │       │   └── index.md
│   │       ├── establishment-picker/
│   │       │   └── index.md
│   │       └── switcher-sso/
│   │           └── index.md
│   ├── edutecnia-models/    # Estructura de modelos
│   │   ├── plugin.json
│   │   └── skills/
│   │       └── models-structure/
│   │           └── index.md
│   └── edutecnia-mobile-ui/ # Componentes móviles compartidos
│       ├── plugin.json
│       └── skills/
│           └── shared-components.md
└── README.md
```

## Plugins Disponibles

### edutecnia-frontend

Sistema de diseño y patrones UI para aplicaciones frontend de Edutecnia.

**Skills:**
- `edutecnia-ui` - Guía completa del sistema de diseño (Vue 2 + Bootstrap-Vue)
- `nuxt4-admin` - Standard para apps Nuxt 4 admin (SSO, Nuxt UI v4, Tailwind v4, composables)
- `establishment-picker` - Flujo de selección de establecimiento post-login (Nuxt 4)
- `switcher-sso` - Switcher cross-app + SSO cookie auth + widget embebible

**Contenido edutecnia-ui:**
- Paleta de colores (naranja #ee7525 como color principal)
- Tipografía y estilos base
- Componentes Bootstrap-Vue personalizados
- Patrones de formularios
- Cards y secciones colapsables
- Botones y badges de estado
- Soporte completo para modo oscuro
- Estructura SCSS con BEM
- Breakpoints responsive

**Contenido nuxt4-admin:**
- Auth SSO cookie + Google OAuth (Firebase)
- Nuxt UI v4 components y layout patterns (Dashboard, Sidebar, Navbar)
- Tailwind CSS v4 custom colors (`:root` pattern)
- CRUD patterns (lista, formulario, detalle, modal)
- Composables base (useApi, useNotification, useConfirmDialog)
- Gotchas conocidos (tree-shaking, UAuthForm, USelect, auth SSR)

**Contenido establishment-picker:**
- Full-screen picker para usuarios multi-establecimiento
- Lógica `navigateAfterLogin()` (1 vs múltiples establecimientos)
- Integración con `useEstablishment()` composable
- Indicador en AppBar con link al picker
- Edge cases y anti-patterns documentados

**Contenido switcher-sso:**
- Arquitectura SSO cookie (JWT, dual auth header/cookie)
- Widget vanilla JS embebible (inline + floating modes)
- Filtrado de apps por permisos/categorías
- Detección de logout global (visibilitychange)
- Variables de entorno (dev vs prod)
- Integración en apps Nuxt 4 (plugin, AppBar container)

**Stack:**
- Vue 2.6 + TypeScript + Bootstrap-Vue (edutecnia-ui)
- Nuxt 4 + Nuxt UI v4 + Tailwind CSS v4 (nuxt4-admin, establishment-picker, switcher-sso)

### edutecnia-mobile-ui

Standard UI para apps móviles de Edutecnia (parents, teachers, students).

**Skills:**
- `mobile-ui-standard` - Patrones completos Ionic Vue + Capacitor

**Contenido:**
- Ionic Vue 8 + Capacitor 7 patterns
- Dark mode (iOS + MD, light/dark/system)
- Page structure, pull-to-refresh, loading skeletons
- Componentes: EduLogo, SelectStudent, LoadingSkeleton
- AppLayout (split pane), AppMenu, AppTopBar
- Modales (modalController + inline)
- Formularios (ion-input stacked, toasts)
- Login card con sombra
- Iconos ionicons (dual ios/md)
- Responsive (576px breakpoint)

**Stack:**
- Vue 3.5 + Ionic Vue 8.4 + Capacitor 7
- Vite 6 + Axios + ionicons

### edutecnia-models

Estructura de modelos y relaciones del backend Edutecnia.

**Skills:**
- `models-structure` - Documentación de modelos y patrones

**Contenido:**
- Modelo central `Establishment` (multi-tenant)
- Estudiantes y matrículas
- Cursos y asignaturas
- Empleados y asignaciones
- Calificaciones y escalas
- Asistencia y observaciones
- Horarios de clases
- Usuarios y permisos
- Patrones de código (soft delete, scopes, validaciones)

**Stack:**
- Ruby on Rails 7.0.8
- Ruby 3.2.2
- PostgreSQL
- Paranoia (soft delete)
- Lockbox (encriptación)

## Instalación

### Opción 1: Desde directorio local

```bash
claude /plugin install /Users/felipe/dev/edutecnia/edutecnia-marketplace
```

### Opción 2: Desde GitHub

```bash
claude /plugin install github:fvegah/edutecnia-marketplace
```

### Instalar plugins específicos

```bash
# Frontend UI
claude /plugin install edutecnia-frontend@edutecnia-marketplace

# Modelos
claude /plugin install edutecnia-models@edutecnia-marketplace
```

## Uso

### Skill de UI (edutecnia-ui)

Activa el skill cuando necesites:
- Crear componentes o páginas para edutecnia-front
- Aplicar el sistema de diseño correcto
- Implementar formularios con el estilo Edutecnia
- Añadir soporte para modo oscuro

```
/edutecnia-frontend:edutecnia-ui
```

### Skill Nuxt 4 Admin (nuxt4-admin)

Activa el skill cuando necesites:
- Crear un nuevo proyecto Nuxt 4 para el ecosistema Edutecnia
- Implementar auth SSO cookie + Google OAuth
- Usar patrones CRUD (lista, formulario, detalle, modal)
- Aplicar Nuxt UI v4 + Tailwind CSS v4 correctamente

```
/edutecnia-frontend:nuxt4-admin
```

### Skill Establishment Picker (establishment-picker)

Activa el skill cuando necesites:
- Implementar selección de establecimiento post-login
- Flujo multi-establecimiento en apps Nuxt 4
- Entender la lógica `navigateAfterLogin()` y el composable `useEstablishment()`

```
/edutecnia-frontend:establishment-picker
```

### Skill Switcher SSO (switcher-sso)

Activa el skill cuando necesites:
- Entender el flujo SSO cookie entre apps
- Integrar el widget switcher en una nueva app
- Implementar detección de logout global
- Configurar variables de entorno para SSO

```
/edutecnia-frontend:switcher-sso
```

### Skill Mobile UI (mobile-ui-standard)

Activa el skill cuando necesites:
- Crear vistas para apps Ionic Vue (parents, teachers, students)
- Implementar dark mode en apps móviles
- Usar patrones de modales, formularios, listas Ionic
- Aplicar estilos y componentes compartidos

```
/edutecnia-mobile-ui:mobile-ui-standard
```

### Skill de Modelos (models-structure)

Activa el skill cuando necesites:
- Crear nuevos modelos en el backend
- Entender las relaciones entre entidades
- Aplicar patrones de código consistentes
- Documentar la estructura de datos

```
/edutecnia-models:models-structure
```

## Colores Principales

| Color | Hex | Uso |
|-------|-----|-----|
| Orange (Primary) | `#ee7525` | Botones, enlaces, acentos |
| Orange Dark | `#d85d0f` | Hover states, gradientes |
| Slate 800 | `#1e293b` | Texto principal, headers |
| Slate 200 | `#e2e8f0` | Bordes, separadores |
| Success | `#059669` | Estados exitosos |
| Danger | `#dc2626` | Errores, eliminación |

## Componentes Destacados

### Cards con Header Colapsable

```scss
.component__section {
  &-header { background: $gradient-dark; }
  &-body { background: white; }
  &--primary &-header { background: $gradient-primary; }
}
```

### Formularios

```scss
.form-control {
  border-radius: 8px;
  border: 1.5px solid $slate-200;
  &:focus { border-color: $edutecnia-orange; }
}
```

### Modo Oscuro

```scss
[data-theme='dark'] {
  .component {
    background: $dark-bg-primary;
    color: $dark-text-primary;
  }
}
```

## Desarrollo

### Agregar un nuevo skill

1. Crear carpeta en `plugins/[plugin-name]/skills/[skill-name]/`
2. Crear `index.md` con el contenido del skill
3. Actualizar `plugin.json` del plugin padre
4. Commit y push

### Estructura del skill

```markdown
---
name: skill-name
description: Descripción corta del skill
---

# Título del Skill

Contenido del skill...
```

## Contribuir

1. Fork del repositorio
2. Crear branch para la feature
3. Commit con mensaje descriptivo
4. Push y crear Pull Request

## Licencia

Propiedad de Edutecnia. Uso interno.

## Contacto

- **Repositorio**: https://github.com/fvegah/edutecnia-marketplace
- **Proyecto principal**: https://github.com/fvegah/edutecnia
