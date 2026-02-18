---
name: staging-server
description: Estado y configuracion del servidor de staging de Edutecnia. DigitalOcean droplet, Puma, Sneakers, Nginx, systemd services, optimizaciones aplicadas.
---

# Servidor Staging — Edutecnia Backend

> **Alcance:** Solo aplica al servidor `back.staging.edutecnia.cl`. Para produccion, revisar la configuracion de deploy por separado.

## Acceso

```bash
ssh deploy@back.staging.edutecnia.cl
```

## Especificaciones del Servidor

| Recurso | Valor |
|---------|-------|
| Proveedor | DigitalOcean (DO-Regular) |
| CPU | 2 cores |
| RAM | 3.8 GB |
| Swap | 2 GB (swappiness=10) |
| Disco | 48 GB (19 GB usados) |
| OS | Ubuntu (systemd) |
| IP | Acceso via DNS |

## Stack de Servicios

| Servicio | Estado | Notas |
|----------|--------|-------|
| Puma (Rails) | Activo | 2 workers (= CPU cores) |
| Sneakers (RabbitMQ) | Activo | Background jobs |
| Nginx | Activo | Reverse proxy, unix socket |
| PostgreSQL local | **Deshabilitado** | Usa DO Managed Database |
| Redis local | **Deshabilitado** | No se usa en staging |
| Promtail | Activo | Envio de logs a Loki |

## Estructura de Deploy (Capistrano)

```
/var/www/edutecnia/
├── current -> releases/YYYYMMDDHHMMSS   # Symlink al release activo
├── releases/                             # Releases historicos
├── shared/
│   ├── .env                              # Variables de entorno (PERSISTENTE)
│   ├── config/
│   │   └── master.key
│   ├── log/
│   │   ├── puma.stdout.log
│   │   ├── puma.stderr.log
│   │   ├── sneakers.stdout.log
│   │   └── sneakers.stderr.log
│   ├── bundle/
│   ├── public/
│   ├── storage/
│   └── tmp/
├── repo/                                 # Repositorio git bare
├── db/                                   # Dumps de base de datos
└── revisions.log                         # Historial de deploys
```

### Importante: current vs shared

- `current/.env` se **sobreescribe** en cada deploy (symlink a nuevo release)
- `shared/.env` **persiste** entre deploys
- Los servicios systemd usan `EnvironmentFile=/var/www/edutecnia/shared/.env`
- Cambios de env vars deben aplicarse en **ambos** archivos, o solo en `shared/.env` si se va a hacer un redeploy pronto

## Servicios Systemd

### Puma (HTTP Server)

```ini
# /etc/systemd/system/puma_edutecnia.service
[Unit]
Description=Puma HTTP Server for edutecnia (Staging)
After=network.target

[Service]
Type=simple
User=deploy
WorkingDirectory=/var/www/edutecnia/current
EnvironmentFile=/var/www/edutecnia/shared/.env
ExecStart=/home/deploy/.rbenv/shims/bundle exec puma
ExecReload=/bin/kill -USR1 $MAINPID
StandardOutput=append:/var/www/edutecnia/shared/log/puma.stdout.log
StandardError=append:/var/www/edutecnia/shared/log/puma.stderr.log
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

Comandos:
```bash
sudo systemctl restart puma_edutecnia.service
sudo systemctl status puma_edutecnia.service
tail -f /var/www/edutecnia/shared/log/puma.stdout.log
```

### Sneakers (Background Jobs)

```ini
# /etc/systemd/system/sneakers_edutecnia.service
[Unit]
Description=Sneakers Background Jobs for edutecnia (Staging)
After=network.target

[Service]
Type=simple
User=deploy
WorkingDirectory=/var/www/edutecnia/current
EnvironmentFile=/var/www/edutecnia/shared/.env
ExecStart=/home/deploy/.rbenv/shims/bundle exec rake sneakers:run
StandardOutput=append:/var/www/edutecnia/shared/log/sneakers.stdout.log
StandardError=append:/var/www/edutecnia/shared/log/sneakers.stderr.log
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

Comandos:
```bash
sudo systemctl restart sneakers_edutecnia.service
sudo systemctl status sneakers_edutecnia.service
tail -f /var/www/edutecnia/shared/log/sneakers.stdout.log
```

## Variables de Entorno Clave

Archivo: `/var/www/edutecnia/shared/.env`

| Variable | Valor | Proposito |
|----------|-------|-----------|
| `RAILS_ENV` | `staging` | Entorno Rails |
| `RAILS_WORKERS` | `2` | Workers Puma (= CPU cores) |
| `RAILS_MAX_THREADS` | `4` | Threads por worker |
| `SSO_COOKIE_DOMAIN` | `.edutecnia.cl` | Dominio cookie SSO |
| `DATABASE_URL` | `postgresql://...ondigitalocean.com:25060/edutecnia_staging` | DO Managed DB (SSL required) |
| `RABBITMQ_URL` | `amqp://...@10.116.0.2:5672` | RabbitMQ en red privada DO |
| `RABBITMQ_EXCHANGE` | `edutecnia_staging` | Exchange dedicado staging |
| `ALLOWED_ORIGINS_CORS` | `portal.staging.edutecnia.cl,...,ayuda-staging.edutecnia.cl,ayuda.edutecnia.cl` | Origenes CORS permitidos |
| `FIREBASE_PROJECT_ID` | `edutecnia-front` | Para Google OAuth |
| `REDIS_HOST` | `localhost` | Redis local (no se usa activamente) |

### SSO_COOKIE_DOMAIN

**Critico**: Debe ser `.edutecnia.cl` (con punto inicial) para que la cookie sea compartida entre todos los subdominios del ecosistema:

- `portal.edutecnia.cl` -> OK
- `ayuda.edutecnia.cl` -> OK
- `ayuda-staging.edutecnia.cl` -> OK
- `rrhh.edutecnia.cl` -> OK

Si se configura como `.staging.edutecnia.cl`, los dominios tipo `ayuda-staging.edutecnia.cl` NO reciben la cookie (no son subdominios de `staging.edutecnia.cl`).

### ALLOWED_ORIGINS_CORS

Debe incluir todos los frontends que hacen requests cross-origin al backend. Si se agrega un nuevo frontend (ej: nueva app Nuxt), agregar su dominio aqui.

## Puma Config (config/puma.rb)

```ruby
workers ENV.fetch("RAILS_WORKERS") { 4 }   # Lee de .env, default 4
threads ENV.fetch("RAILS_MIN_THREADS") { 1 }, ENV.fetch("RAILS_MAX_THREADS") { 5 }.to_i
preload_app!                                 # Reduce memoria (copy-on-write)
bind "unix:///var/www/edutecnia/current/tmp/sockets/server.sock"
worker_timeout ENV.fetch("PUMA_WORKER_TIMEOUT") { 300 }.to_i
```

## Optimizaciones Aplicadas (Feb 2026)

| Cambio | Antes | Despues | Impacto |
|--------|-------|---------|---------|
| Puma workers | 4 | 2 | -670 MB RAM, menos contention CPU |
| Swap | 0 | 2 GB (swappiness=10) | Previene OOM |
| PostgreSQL local | Activo | Deshabilitado | -150 MB RAM (usa DO Managed DB) |
| Redis local | Activo | Deshabilitado | -50 MB RAM (no se usa) |
| systemd EnvironmentFile | No cargaba .env | Carga shared/.env | Puma lee RAILS_WORKERS correctamente |
| SSO_COOKIE_DOMAIN | No definido | `.edutecnia.cl` | Cookie visible en `ayuda-staging.edutecnia.cl` |
| CORS origins | Sin dominios ayuda | Con ayuda-staging y ayuda | Requests cross-origin funcionan |

## Diagnostico Rapido

```bash
# Estado general
ssh deploy@back.staging.edutecnia.cl "uptime && free -h && df -h /"

# Servicios
ssh deploy@back.staging.edutecnia.cl "systemctl status puma_edutecnia sneakers_edutecnia"

# Procesos pesados
ssh deploy@back.staging.edutecnia.cl "ps aux --sort=-%mem | head -10"

# Logs en tiempo real
ssh deploy@back.staging.edutecnia.cl "tail -f /var/www/edutecnia/shared/log/puma.stdout.log"

# Test de latencia del profile
curl -s -o /dev/null -w "HTTP: %{http_code} Time: %{time_total}s\n" \
  --max-time 30 https://back.staging.edutecnia.cl/api/v1/portal/auth/profile \
  -H 'Cookie: edutecnia_token=TOKEN_AQUI'
```

## Problemas Conocidos

### Profile endpoint lento (3-9 segundos)

El endpoint `GET /api/v1/portal/auth/profile` genera ~1.2M allocations por request y tarda 3-9 segundos. Esto es un problema del `AuthController#profile` del backend, no del servidor. Los endpoints de docs (`/api/v1/docs/*`) responden en 20-130ms.

```
profile → 4583ms (ActiveRecord: 484ms | Allocations: 1,226,054)
docs    →   35ms (ActiveRecord:  16ms | Allocations: 4,338)
```

Causa probable: N+1 queries en la carga de permisos/menus/establecimientos del usuario.

### Sneakers alto CPU al iniciar

Sneakers consume 90-100% CPU durante los primeros 15-20 segundos al iniciar (carga Rails + conexion RabbitMQ). Se estabiliza a ~10% despues.

### Dos procesos sneakers

Sneakers forkea un worker hijo. Es normal ver 2 procesos (`master` + `worker`). Si hay mas de 2, reiniciar el servicio.
