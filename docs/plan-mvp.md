# Plan de trabajo: MVP de Garanet (web + app)

Plan para construir desde cero un MVP de Garanet en web y aplicación móvil, en **2 meses trabajando los fines de semana**, simulando una empresa en la que el responsable asume todos los roles. El desarrollo es guiado con IA: Claude implementa y el responsable dirige, revisa y entiende.

- Fecha: 2026-10-17 · actualizado 2026-10-25
- Requerimientos: historias de usuario en Jira (proyecto SCRUM) y en `entregables/historias-usuario/` (fuera de este repositorio)
- Decisiones: [ADR](adr/) · Aprendizajes: [aprendizajes.md](aprendizajes.md)
- Referencia: el repositorio anterior `garanet/` queda **solo como consulta**. No se modifica ni se borra.

## 1. Objetivo

| Aspecto     | Detalle                                                                                                       |
| ----------- | ------------------------------------------------------------------------------------------------------------- |
| Prioridad 1 | Construir bien el MVP web + app y **entender cómo viajan los datos** entre frontend, API, backend y base de datos |
| Prioridad 2 | Ciclo QA completo: pruebas automatizadas (web, API, móvil), rendimiento, seguridad y su CI                    |
| Para qué    | Dar mantenimiento y soporte a los bugs que reporte el ciclo QA, y usar la IA con criterio para las funcionalidades futuras |
| Horizonte   | 2 meses, solo fines de semana (≈ 80 h). Mes 1: MVP. Mes 2: ciclo QA                                           |
| Datos       | Solo sintéticos                                                                                               |
| Costo       | $0                                                                                                            |

## 2. Alcance aprobado (congelado)

| Canal                   | Rol             | Historias                                                                                                                              |
| ----------------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| Web (backoffice)        | Personal tienda | HU-18 ingreso · HU-01 registrar garantía (cliente, producto y factura en el mismo formulario) · HU-07 · HU-02 · HU-03 · HU-05 · HU-06 |
| App (Expo) + portal web | Cliente final   | HU-16 consultar mi garantía (código o QR) · HU-31 fecha de vencimiento                                                                |
| Transversal             | —               | HU-35 versión mínima: logo y colores por tienda (tenant) · web adaptativa en computador, tableta y celular (sección 5)                 |

**Fuera del MVP:** HU-09, HU-12 y HU-13 (los reemplaza el seed sintético), reportes, notificaciones por correo y trazabilidad. Cualquier funcionalidad nueva entra después del MVP.

## 3. La empresa simulada

| Rol             | Responsable                                                                         | Claude                                      |
| --------------- | ----------------------------------------------------------------------------------- | ------------------------------------------- |
| Product Owner   | Elige la historia de Jira y aprueba sus criterios *Given/When/Then*                 | Propone los criterios                       |
| Tech Lead / Dev | Aprueba el plan, revisa el PR y hace el ejercicio **"seguí la petición"**           | Implementa y explica cada pieza brevemente  |
| QA              | Plan de pruebas y automatización (Mes 2)                                            | Guía y revisa las pruebas                   |
| DevOps          | Aprueba y entiende el pipeline de CI                                                | Lo arma y lo explica                        |

### Ciclo de cada historia

```
Historia en Jira → criterios Given/When/Then → Claude implementa en un PR (con pruebas unitarias)
→ revisión + "seguí la petición" → explicación en aprendizajes.md → CI en verde → merge
```

### Ejercicio "seguí la petición" (por historia)

Rastrear una acción de la pantalla hasta la base de datos:

```
Botón (web/app) → DevTools / Network: método, URL y JSON
→ Swagger: endpoint que lo recibe → caso de uso: pasos que ejecuta
→ logs: SQL ejecutado → tabla en la base de datos: fila guardada
```

### Controles de aprendizaje

| # | Control                    | Cómo funciona                                                                                |
| - | -------------------------- | -------------------------------------------------------------------------------------------- |
| 1 | Explicarlo sin mirar       | Cada historia cierra con una explicación propia en `aprendizajes.md`                         |
| 2 | Predecir antes de ejecutar | Antes de probar, se anticipa el resultado (código de estado, fila guardada, pantalla)        |
| 3 | Bugs reales (Mes 2)        | Cada bug del ciclo QA se diagnostica con "seguí la petición" antes de pedir el arreglo a la IA |
| 4 | Regla de oro               | Ningún PR entra si el responsable no puede explicar qué hace y por dónde viajan los datos   |

Los conceptos se aprenden **justo a tiempo**: cuando una historia usa algo nuevo, Claude explica el concepto e indica qué investigar.

Regla: se consulta al responsable antes de iniciar cada fase y cada tarea.

### Auditoría de cada PR (3 capas)

| Capa                | Quién audita                                       | Qué revisa                                                                                   | Cuándo                       |
| ------------------- | -------------------------------------------------- | -------------------------------------------------------------------------------------------- | ---------------------------- |
| 1. Automática       | CI de GitHub, sin IA                               | Compilación, lint, pruebas, CodeQL, Dependabot y escaneo de secretos                         | Cada PR                      |
| 2. IA independiente | Revisor de IA separado del que escribió (4 lentes) | Riesgo/seguridad, confiabilidad, legibilidad y resiliencia                                   | PR de riesgo medio o alto    |
| 3. Responsable      | Responsable                                        | Las 3 preguntas de aprobación                                                                | Cada PR                      |

Para lo crítico (ingreso y aislamiento entre tiendas con RLS) se suma una revisión doble a ciegas.

**Las 3 preguntas de aprobación:**

1. ¿Hace lo que pidió la historia? (criterios *Given/When/Then*)
2. ¿Puedo explicar por dónde viajan los datos?
3. ¿Las capas 1 y 2 están en verde, sin hallazgos bloqueantes?

### Resumen de revisión y guía de prueba (cada PR)

Claude acompaña cada PR con:

| Sección              | Contenido                                                                                       |
| -------------------- | ----------------------------------------------------------------------------------------------- |
| Lo importante        | Dónde enfocar la revisión                                                                       |
| Qué es nuevo         | Novedades respecto de lo ya aprobado                                                            |
| Resumen de todo      | Cada archivo con su propósito                                                                   |
| Verificación propia  | Comprobaciones reales ejecutadas, con su evidencia                                              |
| **Cómo probarlo**    | Pasos para que el responsable compruebe que funciona: qué ejecutar, qué predecir y qué resultado esperar |
| Sugerencias          | Mejoras y si hace falta otra revisión                                                          |

El responsable puede pedir más explicaciones o pruebas antes de aprobar; ningún PR se aprueba con dudas abiertas.

## 4. Requisitos de testeabilidad (desde el inicio)

El MVP se construye para que el ciclo QA del Mes 2 sea posible:

| Requisito                                            | Para qué                                       |
| ---------------------------------------------------- | ---------------------------------------------- |
| `data-testid` en la web y `testID` en la app         | Selectores estables para E2E web y móvil       |
| Endpoint de reinicio del seed (solo entorno de pruebas) | Cada prueba arranca desde datos conocidos   |
| Documentación OpenAPI de cada endpoint               | Pruebas de API y futuros agentes MCP           |
| Datos sintéticos de volumen                          | Pruebas de rendimiento                         |
| Entorno de pruebas separado de la demo               | Probar sin romper la demo                      |

## 5. Diseño existente que se reutiliza

El diseño se aprobó antes del reinicio del proyecto y sigue vigente. No se rediseña: la Fase B parte de estos colores, tipografía y componentes, y cada pantalla del MVP sigue su boceto.

| Elemento                     | Contenido                                                                                              | Enlace                                                        |
| ---------------------------- | ------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------- |
| Identidad y logo             | Logo D2 "Sello verificado": ultramarino `#4b2fe0` + check ámbar `#ffb224`; logotipo en Sora 700        | [Logo](https://claude.ai/artifact/728AprA9VqkW34ds7p3nra)     |
| Sistema de diseño            | Colores, tipografía y 12 componentes React (incluye tablas apiladas en móvil, TabBar y NavRail)        | [Sistema de diseño](https://claude.ai/artifact/C6pguYfSiwpu8R4P9Hzvnd) |
| Flujos                       | Flujos de usuario derivados solo de las historias                                                      | [Flujos](https://claude.ai/artifact/Kx4J6ZL9QminTGQ7R7pjJz)   |
| Bocetos (wireframes)         | Pantallas web y app con paridad de navegación, íconos, nombres y orden                                 | [Bocetos](https://claude.ai/artifact/NxSF2tSaYKQGMiufLmYtCi)  |
| Prototipo navegable          | Recorrido clicable construido con el sistema de diseño                                                 | [Prototipo](https://claude.ai/artifact/GcCJ6ff7o7MvetKqfPP4Jw) |

### Web adaptativa (responsive)

La web se usa desde el navegador del computador, la tableta y el celular, y se adapta según los diseños aprobados:

| Dispositivo | Ancho de pantalla | Navegación                                                                     |
| ----------- | ----------------- | ------------------------------------------------------------------------------ |
| Computador  | ≥ 1024 px         | Barra lateral con texto y contadores de pendientes                             |
| Tableta     | 640–1023 px       | NavRail: columna de 88 px con ícono, etiqueta, badges y "Salir" al pie         |
| Celular     | < 640 px          | Mismo patrón que la app: barra superior y TabBar con 4 destinos + "Más"        |

Reglas táctiles en tableta y celular:

- Campos y botones de al menos 48 px de alto; texto de los campos de 16 px para que el navegador no haga zoom.
- Las tablas se convierten en tarjetas por debajo de 560 px de ancho disponible.
- La acción principal de cada formulario queda fija abajo y a todo el ancho, por encima del TabBar.

Cada historia del MVP se considera terminada solo si funciona en los tres tamaños. En el Mes 2, las pruebas E2E web se ejecutan en los tres anchos.

**Ajustes al alcance del MVP:**

- Los bocetos cubren las 36 historias; en el MVP solo se construyen las pantallas de las historias de la sección 2.
- En la app, el MVP implementa el rol de cliente final (HU-16, HU-31). Las pantallas de la app para el personal de la tienda quedan para después del MVP.
- Los componentes del sistema de diseño son React para web; en la app se recrean con React Native manteniendo colores, tipografía, íconos y nombres (regla de paridad).

## 6. Plan por fin de semana

### Mes 1 — Construir el MVP y entender el flujo de datos

| Fin de semana | Fase                                      | Contenido                                                                          | Demo                                           |
| ------------- | ----------------------------------------- | ---------------------------------------------------------------------------------- | ---------------------------------------------- |
| 1             | A — Arquitectura + B — Fundaciones        | A3 repositorio, A4 multi-tenant, A5 modelo de datos, A6 contrato API, A7 pruebas. Monorepo, BD, API, web, app y CI mínimos, con colores y tipografía del sistema de diseño | Web y app muestran "API en línea"   |
| 2             | 1 — Acceso y marca · 2 — Garantías         | HU-18, HU-35 · HU-01                                                               | Dos tiendas con su marca; registrar una garantía |
| 3             | 3 — Servicio técnico                      | HU-07, HU-02, HU-03, HU-05, HU-06                                                  | Garantía: registro → diagnóstico → finalizada  |
| 4             | 4 — Portal del cliente (app + web)        | HU-16, HU-31, QR                                                                    | **MVP completo en local**                      |

Estado de la Fase A: A1 cerrada → [ADR-001](adr/ADR-001-estilo-de-arquitectura.md) · A2 cerrada → [ADR-002](adr/ADR-002-stack.md) · repositorio `garanet-mvp` creado (público, `main` protegida).

### Mes 2 — Ciclo QA (prioridad 2)

| Fin de semana | Foco                                                        | Herramienta (se confirma en A7)       |
| ------------- | ----------------------------------------------------------- | ------------------------------------- |
| 5             | Despliegue a $0 · plan de pruebas · E2E web                  | Playwright                            |
| 6             | Pruebas de API · pruebas móviles                            | Playwright (API) · Maestro            |
| 7             | Rendimiento · seguridad                                     | k6 · OWASP ZAP + auditoría de dependencias |
| 8             | Todo integrado en el CI · ciclo de bugs (reporte → diagnóstico → arreglo con IA → regresión) | GitHub Actions |

### Después del MVP

- Ecosistema de agentes MCP para orquestar las pruebas.
- Historias fuera del MVP, implementadas con IA y con criterio.

## 7. Costos a validar antes de usarlos

| Elemento                                | Costo                 | Decisión                                   |
| --------------------------------------- | --------------------- | ------------------------------------------ |
| Publicación en App Store                | USD 99 al año         | No; se usa Expo Go / EAS gratis            |
| Publicación en Google Play              | USD 25 un solo pago   | No; se usa Expo Go / EAS gratis            |
| Alojamiento de API, web y base de datos | Depende del proveedor | Se elige en el fin de semana 5, solo planes gratuitos |
