# Edutecnia Mobile UI - Componentes Compartidos

Componentes UI compartidos para las 3 aplicaciones moviles de Edutecnia:
- `edutecnia-students-app` - App de estudiantes
- `edutecnia-parents-app` - App de apoderados
- `edutecnia-teachers-app` - App de docentes

## Stack Tecnologico

Todas las apps usan:
- **Vue**: 3.5.x
- **Ionic**: 8.4.x
- **Vite**: 6.x
- **TypeScript**: 5.x
- **Capacitor**: 7.x

---

## Componentes Compartidos

### 1. EduLogo - Logo con Indicador de Ambiente

Componente que muestra el logo de Edutecnia con indicador visual del ambiente (desarrollo, staging, produccion).

**Ubicacion**: `src/components/common/EduLogo.vue`

**Props**:
| Prop | Tipo | Default | Descripcion |
|------|------|---------|-------------|
| `size` | `'small' \| 'medium' \| 'large'` | `'medium'` | Tamano del logo |
| `showEnvironment` | `boolean` | `true` | Mostrar badge de ambiente |

**Codigo del Componente**:

```vue
<template>
  <div class="edu-logo" :class="[`edu-logo--${size}`]">
    <img
      src="@/assets/edutecnia-logo.svg"
      alt="Edutecnia"
      class="edu-logo__image"
    />
    <span
      v-if="showEnvironment && environmentLabel"
      class="edu-logo__badge"
      :class="[`edu-logo__badge--${environment}`]"
    >
      {{ environmentLabel }}
    </span>
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue';

interface Props {
  size?: 'small' | 'medium' | 'large';
  showEnvironment?: boolean;
}

const props = withDefaults(defineProps<Props>(), {
  size: 'medium',
  showEnvironment: true
});

const environment = computed(() => {
  const apiUrl = import.meta.env.VITE_API_URL || '';

  if (apiUrl.includes('localhost') || apiUrl.includes('127.0.0.1')) {
    return 'development';
  }
  if (apiUrl.includes('staging')) {
    return 'staging';
  }
  return 'production';
});

const environmentLabel = computed(() => {
  switch (environment.value) {
    case 'development':
      return 'DESARROLLO';
    case 'staging':
      return 'STAGING';
    default:
      return null; // No mostrar en produccion
  }
});
</script>

<style scoped>
.edu-logo {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 4px;
}

.edu-logo__image {
  display: block;
  height: auto;
}

/* Tamanos */
.edu-logo--small .edu-logo__image {
  max-width: 120px;
}

.edu-logo--medium .edu-logo__image {
  max-width: 180px;
}

.edu-logo--large .edu-logo__image {
  max-width: 240px;
}

/* Badge de ambiente */
.edu-logo__badge {
  font-size: 10px;
  font-weight: 700;
  letter-spacing: 1px;
  padding: 2px 8px;
  border-radius: 4px;
  text-transform: uppercase;
}

.edu-logo__badge--development {
  background-color: #ff6b6b;
  color: white;
}

.edu-logo__badge--staging {
  background-color: #ffd43b;
  color: #333;
}
</style>
```

**Uso**:

```vue
<!-- En Login -->
<EduLogo size="large" />

<!-- En Menu Lateral -->
<EduLogo size="medium" />

<!-- Sin badge de ambiente -->
<EduLogo size="small" :show-environment="false" />
```

---

### 2. Estructura de Login Estandar

El login debe seguir esta estructura para consistencia visual:

```vue
<template>
  <ion-page>
    <ion-content :fullscreen="true" class="login-color-content">
      <div id="container-login">
        <ion-grid>
          <ion-card class="login-card">
            <!-- Logo con ambiente -->
            <div class="login-img-logo">
              <EduLogo size="large" />
            </div>

            <ion-card-header>
              <ion-card-subtitle>{{ appSubtitle }}</ion-card-subtitle>
              <ion-card-title>Iniciar Sesion</ion-card-title>
            </ion-card-header>

            <ion-card-content id="form-login">
              <!-- Form content -->
            </ion-card-content>
          </ion-card>
        </ion-grid>
      </div>
    </ion-content>
  </ion-page>
</template>
```

**Subtitulos por App**:
- Students: `"App estudiantes"`
- Parents: `"App apoderados"`
- Teachers: `"App docentes"`

---

### 3. CSS Compartido - Variables

Archivo: `src/theme/shared-variables.css`

```css
:root {
  /* Colores primarios Edutecnia */
  --edu-primary: #ee7525;
  --edu-primary-rgb: 238, 117, 37;
  --edu-primary-dark: #d96820;
  --edu-primary-light: #ff8c42;

  /* Colores de ambiente */
  --edu-env-development: #ff6b6b;
  --edu-env-staging: #ffd43b;
  --edu-env-production: transparent;

  /* Login card */
  --edu-login-card-shadow: 4px 4px 0 2px #112327;
  --edu-login-card-radius: 8px;

  /* Espaciado */
  --edu-spacing-xs: 4px;
  --edu-spacing-sm: 8px;
  --edu-spacing-md: 16px;
  --edu-spacing-lg: 24px;
  --edu-spacing-xl: 32px;
}

/* Dark mode overrides */
@media (prefers-color-scheme: dark) {
  :root {
    --edu-login-card-shadow: 4px 4px 0 2px rgba(255, 255, 255, 0.1);
  }
}
```

---

### 4. Menu Lateral - Integracion de Logo

El `AppMenu.vue` debe usar `EduLogo` en lugar de img directo:

```vue
<!-- Antes -->
<img src="../../assets/edutecnia-logo.svg" />

<!-- Despues -->
<EduLogo size="medium" />
```

---

## Checklist de Implementacion

Al crear o modificar estas apps, asegurar:

- [ ] Componente `EduLogo.vue` existe en `src/components/common/`
- [ ] Login usa `<EduLogo size="large" />`
- [ ] Menu lateral usa `<EduLogo size="medium" />`
- [ ] Variable `VITE_API_URL` configurada en `.env`
- [ ] Badge de ambiente visible en desarrollo/staging
- [ ] Badge NO visible en produccion

---

## Configuracion de Ambientes

### .env.development
```env
VITE_API_URL=http://localhost:3000
```

### .env.staging
```env
VITE_API_URL=https://back.staging.edutecnia.cl
```

### .env.production
```env
VITE_API_URL=https://back.edutecnia.cl
```

---

## Proximos Pasos (Roadmap)

1. **Fase 1** (Actual): EduLogo compartido
2. **Fase 2**: Componentes de formulario (RutInput, PasswordInput)
3. **Fase 3**: Composable de autenticacion (`useAuth`)
4. **Fase 4**: Migrar a monorepo o npm package privado
