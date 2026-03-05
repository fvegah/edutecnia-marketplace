# Edutecnia Marketplace

## ⛔ REGLA CRÍTICA - FLUJO DE TRABAJO GIT

### Ramas Protegidas (CI/CD Automático)
Las siguientes ramas tienen **pipelines automáticos** y **NUNCA** se debe:
- ❌ Hacer push automático sin autorización explícita
- ❌ Trabajar directamente en ellas (excepto hotfix urgente)
- ❌ Hacer commits directos

| Rama | Entorno | Deploy |
|------|---------|--------|
| `main` / `master` | Producción | Automático |
| `staging` | Staging/QA | Automático |

### Rama de Desarrollo
- ✅ **`develop`** — Rama principal de trabajo

### Flujo de Trabajo
```
develop → staging (probar) → main/master (producción)
```

### Requiere autorización EXPLÍCITA:
- ❌ `git push` a `main`/`master`/`staging`
- ❌ `git push` a `develop` (preguntar primero)

**Esta regla tiene MÁXIMA PRIORIDAD.**

---

## Descripción
Marketplace de recursos educativos de Edutecnia.

## Comandos
```bash
npm run dev          # Desarrollo
npm run build        # Build producción
```
