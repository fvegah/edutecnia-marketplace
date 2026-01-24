# Edutecnia Marketplace

Marketplace de plugins Claude Code para el ecosistema Edutecnia. Contiene skills de diseño UI, patrones de modelos y guías de desarrollo para aplicaciones educativas.

## Estructura

```
edutecnia-marketplace/
├── .claude-plugin/
│   └── plugin.json          # Configuración del marketplace
├── plugins/
│   ├── edutecnia-frontend/  # Sistema de diseño UI
│   │   ├── plugin.json
│   │   └── skills/
│   │       └── edutecnia-ui/
│   │           └── index.md
│   └── edutecnia-models/    # Estructura de modelos
│       ├── plugin.json
│       └── skills/
│           └── models-structure/
│               └── index.md
└── README.md
```

## Plugins Disponibles

### edutecnia-frontend

Sistema de diseño y patrones UI para aplicaciones frontend de Edutecnia.

**Skills:**
- `edutecnia-ui` - Guía completa del sistema de diseño

**Contenido:**
- Paleta de colores (naranja #ee7525 como color principal)
- Tipografía y estilos base
- Componentes Bootstrap-Vue personalizados
- Patrones de formularios
- Cards y secciones colapsables
- Botones y badges de estado
- Soporte completo para modo oscuro
- Estructura SCSS con BEM
- Breakpoints responsive

**Stack:**
- Vue 2.6 + TypeScript
- Bootstrap-Vue
- SCSS con metodología BEM
- Font Awesome 4.x

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
