# SECURE REMOTE EXECUTION 

Organización académica para el desarrollo del **Secure Product Challenge**, un reto acumulativo del curso **FDSI (Fundamentos de Seguridad Informática)** — Escuela Colombiana de Ingeniería Julio Garavito.

## Sobre el proyecto

Construimos un prototipo que resuelve una problemática real de administración de infraestructura de red:

> La administración manual de firewalls, routers y switches genera errores de configuración, falta de estandarización, cambios sin evidencia y dificultad para determinar quién ejecutó una acción o cuál fue su resultado.

El sistema permite **consultar un inventario ficticio de dispositivos de red** y **preparar la ejecución controlada de scripts aprobados**, evolucionando de forma incremental a través de cada laboratorio del reto:

| Laboratorio | Enfoque | Estado |
|---|---|---|
| **Lab 3** | HTTP público sin autenticación — reconocimiento, telemetría y hardening básico | ✅ |
| **Lab 4** | HTTPS, identidad, sesiones, autenticación y roles | 🔜 |
| **Lab 5** | DevSecOps y supply chain (SAST, SCA, SBOM, contenedores) | 🔜 |
| **Lab 6** | Cloud Purple Team (DAST autenticado, WAF, observabilidad) | 🔜 |

## Arquitectura

El sistema está construido como **microservicios**, separados en repositorios independientes:

| Repositorio | Descripción | Stack |
|---|---|---|
| [`backend`](../backend) | API de inventario de dispositivos y simulación de scripts | Java 21 · Spring Boot · PostgreSQL |
| [`frontend`](../frontend) | Interfaz web para consultar el inventario y simular ejecuciones | React · Vite · TypeScript |

```
Usuario ──HTTP──▶ Nginx ──▶ Frontend (React)
                       └──▶ /api ──▶ Backend (Spring Boot) ──▶ PostgreSQL
```

## Alcance de seguridad (importante)

Este es un proyecto **académico con datos ficticios**. Durante el Laboratorio 3:
- No se realizan cambios reales sobre dispositivos.
- Toda "ejecución de script" es simulada y solo se registra como evidencia.
- No hay autenticación ni cifrado TLS — esto es intencional y forma parte del ejercicio de Red Team / Blue Team; se corrige en el Laboratorio 4.

## Equipo

- **Tomás Olaya Díaz** — [GitHub](https://github.com/)
- **Juan Pablo Vega Villamil** — [GitHub](https://github.com/)

## Recursos

- Guía del laboratorio: `Secure Product Challenge | FDSI`
- Registro de riesgos y evidencia: ver `risk-register.md` y carpetas `evidence/` en el repo backend

---

*Escuela Colombiana de Ingeniería Julio Garavito — 2026-2*
