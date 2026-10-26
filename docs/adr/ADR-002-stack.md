# ADR-002: Stack tecnológico

- Estado: Aprobado
- Fecha: 2026-10-25
- Decide: responsable del proyecto
- Relacionado: [ADR-001](ADR-001-estilo-de-arquitectura.md)

## Contexto

El MVP debe construirse en 2 meses, en web y app con paridad de experiencia, para varias tiendas (multi-tenant), con herramientas de costo $0. El desarrollo es guiado con IA: Claude implementa y el responsable revisa cada PR y rastrea cómo viajan los datos de punta a punta. Después del MVP, el sistema es la base del ciclo QA (pruebas web, API, móvil, rendimiento y seguridad).

Por eso el stack debe:

- usar la menor cantidad posible de lenguajes, para que la revisión sea viable;
- facilitar la arquitectura del ADR-001 (puertos y adaptadores);
- aislar los datos de cada tienda;
- estar alineado con tecnologías usadas en la industria.

## Decisión

**TypeScript en todas las capas.**

| Componente     | Elección                 | Motivo                                                                                 | Madurez |
| -------------- | ------------------------ | -------------------------------------------------------------------------------------- | ------- |
| Lenguaje       | TypeScript               | Un solo lenguaje para backend, web, app y pruebas                                      | 🟢      |
| Backend        | NestJS                   | Su inyección de dependencias conecta los adaptadores con los puertos del ADR-001       | 🟢      |
| Base de datos  | PostgreSQL               | Row Level Security (RLS): la base de datos aísla los datos de cada tienda              | 🟢      |
| Acceso a la BD | Drizzle                  | Consultas cercanas a SQL, legibles al rastrear una petición                            | 🟡      |
| Validación     | Zod                      | Reglas definidas una vez y compartidas por la API, la web y la app                     | 🟢      |
| Web            | React + Vite             | Mismo modelo mental que la app; el backoffice no necesita SEO                          | 🟢      |
| App            | Expo (React Native)      | Una sola base de código para iOS y Android                                             | 🟢      |
| Autenticación  | Better Auth              | Gratis, se ejecuta en el propio servidor, incluye 2FA                                  | 🟡      |
| Repositorio    | Monorepo con pnpm        | Un cambio de punta a punta cabe en un solo PR                                          | 🟢      |
| Pruebas        | Vitest (unitarias)       | Rápido para probar el dominio; el resto de herramientas se confirma en A7              | 🟢      |
| CI             | GitHub Actions           | Gratis e ilimitado en repositorios públicos                                            | 🟢      |

🟢 estándar consolidado · 🟡 herramienta joven

### Componentes jóvenes (🟡)

Drizzle y Better Auth se mantienen a propósito. Ambos quedan detrás de adaptadores (ADR-001), por lo que reemplazarlos solo afecta `infrastructure/` y no las reglas del negocio.

## Alternativas descartadas

| Alternativa                 | Motivo                                                                          |
| --------------------------- | ------------------------------------------------------------------------------- |
| Java / C# en el backend     | Un segundo lenguaje que revisar y mantener                                      |
| Express                     | La estructura de puertos y adaptadores se armaría a mano                        |
| MongoDB                     | Aislamiento por tienda más débil; los datos de garantías son relacionales       |
| Prisma / TypeORM            | Más difundidos en empresas, pero ocultan más el SQL                             |
| Next.js                     | Agrega lógica de servidor que el backoffice no necesita                         |
| Swift + Kotlin              | Dos lenguajes y dos aplicaciones separadas                                      |
| Auth0 / Okta                | Costo al crecer                                                                 |
| Keycloak                    | Gratis, pero exige otro servidor y mucha configuración para el plazo de 2 meses |
| Repositorios separados      | Un cambio de punta a punta requeriría varios PR                                 |

## Consecuencias

- ✅ Un solo lenguaje desde la pantalla hasta la base de datos.
- ✅ Tecnologías mayoritariamente estándar en la industria.
- ✅ Costo $0.
- ⚠️ NestJS usa decoradores e inyección de dependencias, que requieren explicación al revisar.
- ⚠️ Drizzle y Better Auth tienen menos trayectoria en empresas grandes.
