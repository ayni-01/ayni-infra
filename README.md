# Somos Ayni — Infraestructura (Render)

Repositorio de infraestructura para desplegar los 8 microservicios de Somos Ayni en [Render](https://render.com) con un solo clic usando el Blueprint `render.yaml`.

---

## Despliegue en Render (1 clic)

[![Deploy to Render](https://render.com/images/deploy-to-render-button.svg)](https://render.com/deploy?repo=https://github.com/ayni-01/ayni-infra)

O manualmente:

1. Ir a [render.com/dashboard](https://dashboard.render.com)
2. **New → Blueprint**
3. Conectar este repositorio (`ayni-01/ayni-infra`)
4. Render detecta el `render.yaml` y muestra los 9 recursos (1 DB + 8 servicios)
5. Hacer clic en **Apply** → Render crea todo automáticamente

---

## Paso obligatorio post-despliegue

El `JWT_SECRET` está marcado como `sync: false` (secreto manual). Después de que Render cree los servicios:

1. Ir a cada servicio en el dashboard → **Environment**
2. Agregar la variable:
   ```
   JWT_SECRET = somosayni-jwt-secret-key-que-debe-ser-muy-larga-para-hs256
   ```

   > El valor debe ser **idéntico** en los 8 servicios. Si difiere, los tokens de `identidad-service` serán rechazados por los demás.

   Además, solo en `ayni-asistente-ia-service`:
   ```
   OPENAI_API_KEY = <tu api key de platform.openai.com>
   ```

3. Render redesplegará cada servicio automáticamente al guardar.

---

## Recursos creados

| Recurso | Tipo | Plan |
|---|---|---|
| `ayni-postgres` | PostgreSQL 16 | free (expira 90 días) |
| `ayni-identidad-service` | Web Service (Docker) | free |
| `ayni-perfiles-service` | Web Service (Docker) | free |
| `ayni-retos-service` | Web Service (Docker) | free |
| `ayni-postulaciones-service` | Web Service (Docker) | free |
| `ayni-habilidades-service` | Web Service (Docker) | free |
| `ayni-notificaciones-service` | Web Service (Docker) | free |
| `ayni-metricas-service` | Web Service (Docker) | free |
| `ayni-asistente-ia-service` | Web Service (Docker) | free |

---

## URLs de producción

Una vez desplegado, Render asigna URLs del tipo:

| Servicio | URL |
|---|---|
| identidad | `https://ayni-identidad-service.onrender.com` |
| perfiles | `https://ayni-perfiles-service.onrender.com` |
| retos | `https://ayni-retos-service.onrender.com` |
| postulaciones | `https://ayni-postulaciones-service.onrender.com` |
| habilidades | `https://ayni-habilidades-service.onrender.com` |
| notificaciones | `https://ayni-notificaciones-service.onrender.com` |
| metricas | `https://ayni-metricas-service.onrender.com` |
| asistente-ia | `https://ayni-asistente-ia-service.onrender.com` |

---

## Limitaciones del plan gratuito

| Limitación | Detalle |
|---|---|
| **Cold start** | Los servicios se duermen tras 15 min sin tráfico. El primer request tarda ~60 seg en Java |
| **RAM** | 512 MB por servicio (Spring Boot necesita ~400 MB mínimo) |
| **PostgreSQL** | Expira automáticamente a los **90 días** |
| **Build time** | El multi-stage Docker build puede tardar 5-10 min por servicio |

### Para producción real — cambiar en `render.yaml`:

```yaml
plan: starter   # $7/mes por servicio — no se duermen, más RAM
```

Con 9 recursos en Starter: ~$63/mes.

---

## Variables de entorno

Render inyecta automáticamente todas las variables de DB desde el `fromDatabase`. Solo tienes que configurar manualmente:

| Variable | Dónde configurar | Valor |
|---|---|---|
| `JWT_SECRET` | Dashboard → cada servicio → Environment | Mismo valor en los 8 |
| `OPENAI_API_KEY` | Dashboard → `ayni-asistente-ia-service` → Environment | Solo en ese servicio |
