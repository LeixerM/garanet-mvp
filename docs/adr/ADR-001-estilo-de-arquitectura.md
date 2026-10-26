# ADR-001: Estilo de arquitectura

- Estado: Aprobado
- Fecha: 2026-10-17
- Decide: responsable del proyecto

## Contexto

Garanet es un SaaS de garantías multi-tenant con clientes web y móvil que consumen una misma API. El responsable va a construirlo desde cero, mantenerlo y agregarle funcionalidades durante años. Por eso se necesita una organización que:

- permita agregar funcionalidades sin romper las existentes;
- permita probar las reglas del negocio sin base de datos;
- haga fácil encontrar todo lo relacionado con una funcionalidad al dar soporte.

## Decisión

**Screaming afuera + Hexagonal adentro.**

- **Screaming:** las carpetas de primer nivel se nombran por funcionalidad del negocio (`warranties/`, `customers/`, `auth/`).
- **Hexagonal:** dentro de cada funcionalidad hay tres capas, y las dependencias siempre apuntan hacia el dominio:

```
src/
  warranties/
    domain/          → reglas puras; no conoce nada externo
    application/     → casos de uso y puertos (contratos)
    infrastructure/  → adaptadores: base de datos, HTTP
```

Regla de criterio: solo se crea un puerto cuando hay una dependencia externa (base de datos, correo, archivos). No se crean interfaces sin necesidad.

El sistema completo sigue siendo **cliente-servidor** (web y app → API). MVC puede aparecer como patrón interno de la capa HTTP; no compite con esta decisión.

## Alternativas descartadas

| Alternativa          | Motivo                                                                                       |
| -------------------- | -------------------------------------------------------------------------------------------- |
| Capas clásicas       | El dominio depende de la base de datos; migrar después a hexagonal es caro con clientes reales |
| Hexagonal sola       | Sin Screaming, las carpetas se agrupan por capa técnica y cada funcionalidad queda dispersa  |
| Solo Screaming       | No define cómo separar responsabilidades dentro de cada funcionalidad                        |

## Consecuencias

- ✅ Las reglas del negocio se prueban rápido, sin base de datos.
- ✅ Una funcionalidad nueva es una carpeta nueva.
- ✅ Cambiar una pieza externa no toca el dominio.
- ⚠️ Más archivos por funcionalidad al principio.
- ⚠️ Requiere estudiar inversión de dependencias (la "D" de SOLID).
