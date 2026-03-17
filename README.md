# 🚚 Nexiogen — Plataforma SaaS de Última Milla

> Sistema multi-tenant de logística y gestión de entregas con optimizador de rutas VRP, prueba de entrega (POD) móvil y dashboard en tiempo real.

[![Python](https://img.shields.io/badge/Python-3.11-3776AB?style=flat&logo=python&logoColor=white)](https://python.org)
[![FastAPI](https://img.shields.io/badge/FastAPI-009688?style=flat&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16-316192?style=flat&logo=postgresql&logoColor=white)](https://postgresql.org)
[![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?style=flat&logo=docker&logoColor=white)](https://docker.com)
[![Live](https://img.shields.io/badge/Demo-nexiogen.com-brightgreen?style=flat)](https://nexiogen.com)

---

## 📌 ¿Qué es Nexiogen?

Nexiogen es una plataforma SaaS multi-tenant para gestión de repartos de última milla. Optimiza rutas automáticamente, registra prueba de entrega (POD) desde el móvil y monitorea el estado en tiempo real.

**Caso de uso inicial:** Corporación Municipal — entregas de beneficios sociales a domicilio.

---

## 🏗️ Arquitectura

| Componente | Tecnología | Detalle |
|------------|-----------|---------|
| API Backend | FastAPI + Uvicorn | 2 workers, Python 3.11 |
| Base de datos | PostgreSQL 16 | Schema core.*, puerto 5433 |
| Auth | JWT + bcrypt | Access token + refresh token |
| Multi-tenant | RLS PostgreSQL | Aislamiento por organizacion_id |
| Optimizador | Clarke-Wright VRP | Python puro, sin deps externas |
| Dashboard | HTML5 + Leaflet.js | Archivo único 13KB, sin build |
| POD Móvil | HTML5 (pod.html) | GPS + foto + firma canvas |
| Proxy | Nginx + Cloudflare | proxy_http_version 1.1 |
| Deploy | Docker Compose | restart: always |

---

## 🌐 Dominios en Producción

| Dominio | Destino | Estado |
|---------|---------|--------|
| app.nexiogen.com | Dashboard HTML v3 | ✅ Live |
| api.nexiogen.com | FastAPI puerto 8003 | ✅ Live |
| nexiogen.com | Redirect a app | ✅ Live |

---

## ⚡ API — 12 Endpoints Activos

### Autenticación
- POST /auth/login — Email + password → JWT access + refresh token
- GET /auth/me — Usuario autenticado desde JWT
- POST /auth/refresh — Nuevo access token desde refresh
- POST /auth/usuarios — Crear usuario (solo admin_comercio)

### Entregas y Rutas
- GET /entregas/ — Lista filtrada por organizacion_id (RLS)
- PATCH /entregas/{id}/estado — Cambiar estado con evento
- GET /entregas/{id}/historico — Timeline de eventos
- GET/POST /rutas — CRUD de rutas

### Optimizador VRP
- POST /api/v1/optimizar — Clarke-Wright VRP, preview o guardar
- GET /api/v1/optimizar/preview — Preview rápido

### POD — Prueba de Entrega
- POST /api/v1/pods — Registrar POD (foto + firma + GPS)
- GET /api/v1/pods/{ruta_id} — Consultar PODs de una ruta

Docs interactivos: api.nexiogen.com/docs

---

## 🧠 Optimizador Clarke-Wright VRP

Python puro, sin dependencias externas. Implementado en services/optimizador.py

- Múltiples vehículos con capacidad_kg diferente
- Ventanas horarias — incompatibilidad mayor a 4h rechaza fusión
- Priorización: urgente+perecible > urgente > perecible > normal
- Agrupación por comuna con bonus de savings
- Balance de carga automático entre vehículos
- Nearest-neighbor para secuencia óptima de paradas

Prueba con 10 entregas en Santiago: 1 ruta, 65.35 km, ~4.5 horas

---

## 📱 Módulo POD — Prueba de Entrega Móvil

Sin app nativa. Funciona desde el navegador móvil del repartidor.

- GPS via navigator.geolocation (precisión ±8m típica)
- Foto via input capture=environment (cámara trasera)
- Firma digital en canvas HTML5 con soporte touch/mouse
- Datos del receptor: nombre, estado de entrega, observaciones
- Barra de 4 pasos — botón envío activo al completarlos todos

---

## 🗄️ Schema de Base de Datos (core.*)

| Tabla | Descripción |
|-------|-------------|
| core.entrega | Entregas con estado, GPS, peso, ventanas horarias |
| core.ruta | Rutas con transporte, distancia, depósito |
| core.transporte | Vehículos con capacidad_kg |
| core.beneficiario | Destinatarios con dirección y GPS |
| core.usuario | Usuarios con rol y organizacion_id |
| core.entrega_evento | Timeline de cambios de estado |
| core.pods | Pruebas de entrega (foto, firma, GPS, timestamp) |

---

## 🖥️ Dashboard app.nexiogen.com

HTML único de 13KB — Nginx lo sirve directamente, sin build.

- Login JWT con auto-login
- 5 KPIs + gráfico distribución de estados
- Optimizar rutas — modal con fecha, depósito, toggle preview
- Tabla de entregas con filtros + panel detalle lateral
- Historial de eventos por entrega (timeline)
- Mapa Leaflet: marcadores numerados por orden de visita
- Líneas de ruta coloreadas por vehículo + ícono depósito

---

## 🗺️ Roadmap

- [x] VPS + Docker Compose + Nginx + Cloudflare
- [x] FastAPI 2 workers + JWT + RLS multi-tenant
- [x] Optimizador Clarke-Wright VRP
- [x] Dashboard HTML v3 + mapa Leaflet
- [x] Módulo POD — GPS + foto + firma digital
- [x] 12 endpoints activos en producción
- [ ] Cola offline para PODs sin señal
- [ ] Reporte PDF por ruta
- [ ] Dashboard tiempo real con WebSocket
- [ ] Panel administración multi-tenant
- [ ] Onboarding SaaS completo

---

## 👤 Autor

**Luis Fuenzalida** — [@fuenliza64](https://github.com/fuenliza64)
fuenliza64@gmail.com | [nexiogen.com](https://nexiogen.com)

---

MIT License
