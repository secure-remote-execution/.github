# SECURE REMOTE EXECUTION

Organización académica para el desarrollo del **Secure Product Challenge**, un reto acumulativo del curso **FDSI (Fundamentos de Seguridad Informática)** — Escuela Colombiana de Ingeniería Julio Garavito.

## Sobre el proyecto

Construimos un prototipo que resuelve una problemática real de administración de infraestructura de red:

> La administración manual de firewalls, routers y switches genera errores de configuración, falta de estandarización, cambios sin evidencia y dificultad para determinar quién ejecutó una acción o cuál fue su resultado.

El sistema permite **consultar un inventario ficticio de dispositivos de red** y **preparar la ejecución controlada de scripts aprobados**, evolucionando de forma incremental a través de cada laboratorio del reto:

| Laboratorio | Enfoque | Estado | Informe |
|---|---|---|---|
| **Lab 3** | HTTP público sin autenticación — reconocimiento, telemetría y hardening básico | ✅ | [Ver informe](https://github.com/secure-remote-execution/backend/blob/main/docs/labs/FDSI-Lab3.md) |
| **Lab 4** | HTTPS, identidad, sesiones, autenticación y roles | 🔜 | — |
| **Lab 5** | DevSecOps y supply chain (SAST, SCA, SBOM, contenedores) | 🔜 | — |
| **Lab 6** | Cloud Purple Team (DAST autenticado, WAF, observabilidad) | 🔜 | — |

## Arquitectura

El sistema está construido como **microservicios**, separados en repositorios independientes:

| Repositorio | Descripción | Stack |
|---|---|---|
| [`backend`](../backend) | API de inventario de dispositivos y simulación de scripts | Java 21 · Spring Boot · PostgreSQL |
| [`frontend`](../frontend) | Interfaz web para consultar el inventario y simular ejecuciones | React · Vite · TypeScript | 

''' 
Usuario ──HTTP──▶ Nginx ──▶ Frontend (React)
└──▶ /api ──▶ Backend (Spring Boot) ──▶ PostgreSQL 
''' 


## Diagramas

<p align="center">
  <img src="https://github.com/user-attachments/assets/75485103-1acf-4bac-ba88-b493e99dc933" width="600" alt="DFD del sistema, Laboratorio 3"/>
</p>
<p align="center"><em>Diagrama de flujo de datos, Laboratorio 3</em></p>

## Matriz de Amenazas (STRIDE)

| Objeto | S | T | R | I | D | E | Descripción | Validez |
|---|:---:|:---:|:---:|:---:|:---:|:---:|---|---|
| Usuario anónimo → Aplicación web (GET /) | | X | X | X | | | El tráfico HTTP no cifrado permite leer y potencialmente alterar el contenido de la carga inicial en tránsito. Nmap y curl revelaron la versión exacta del servidor y el HTML completo. Sin autenticación, no es posible atribuir una solicitud a un usuario identificable. | Confirmada con evidencia (Nmap, curl) |
| Navegador → API pública (GET /api/devices) | | X | X | X | | | El inventario de dispositivos se consulta sobre el mismo canal HTTP sin cifrar, por lo que los datos de cada dispositivo viajan legibles y podrían alterarse en tránsito. Al no existir autenticación, no hay forma de saber quién realizó la consulta. | Confirmada por extensión del mismo protocolo sin cifrar |
| Navegador → API pública (POST /api/scripts/simulate) | | X | X | X | | | La captura con Wireshark del POST mostró el cuerpo JSON completo en texto plano, con el identificador del dispositivo, el script y los parámetros legibles sin cifrado alguno. | Confirmada con evidencia (Wireshark) |
| Aplicación web / Nginx (cabeceras de respuesta) | | | | X | | | El análisis pasivo con ZAP encontró ausencia de Content-Security-Policy, ausencia de protección contra clickjacking, el header Server exponiendo la versión exacta de Nginx, y falta de X-Content-Type-Options. | Confirmada con evidencia (OWASP ZAP) |
| Nginx (proxy interno) → Backend Spring Boot | | | | | | | Comunicación entre Nginx y el backend sobre localhost en el puerto 8081. No está expuesta fuera del servidor, por lo que no se evaluó de forma directa. | No evaluada en este laboratorio |
| API pública → PostgreSQL | | | | | | | Consultas entre el backend y la base de datos de inventario. Este flujo no está expuesto fuera del servidor, por lo que no se evaluó de forma directa durante el reconocimiento de este laboratorio. | No evaluada en este laboratorio |
| Simulador de scripts (control de autorización) | X | | | | | X | Al no existir identidad ni roles, cualquier persona anónima puede invocar la simulación con el mismo nivel de acceso que tendría un operador autorizado, y puede declarar cualquier deviceId o scriptName sin que el sistema verifique quién lo solicita. | Identificada como riesgo de diseño, no ejecutada como prueba técnica en este laboratorio |

**S:** Spoofing · **T:** Tampering · **R:** Repudiation · **I:** Information Disclosure · **D:** Denial of Service · **E:** Elevation of Privilege

> No se evaluó Denial of Service en ningún flujo, ya que la guía del laboratorio prohíbe explícitamente ejecutar pruebas de denegación de servicio contra el objetivo.
## Alcance de seguridad (importante)

Este es un proyecto **académico con datos ficticios**. Durante el Laboratorio 3:
- No se realizan cambios reales sobre dispositivos.
- Toda "ejecución de script" es simulada y solo se registra como evidencia.
- No hay autenticación ni cifrado TLS — esto es intencional y forma parte del ejercicio de Red Team / Blue Team; se corrige en el Laboratorio 4.

## Equipo

- **Tomás Olaya Díaz** — [GitHub](https://github.com/iAxstral)
- **Juan Pablo Vega Villamil** — [GitHub](https://github.com/JDeltax)

## Recursos

- Guía del laboratorio: `Secure Product Challenge | FDSI`
- Informes de cada laboratorio: ver [`docs/`](https://github.com/secure-remote-execution/backend/tree/main/docs) en el repo backend
- Registro de riesgos y evidencia: ver `risk-register.md` y carpetas `evidence/` en el repo backend

---

*Escuela Colombiana de Ingeniería Julio Garavito — 2026-2*
