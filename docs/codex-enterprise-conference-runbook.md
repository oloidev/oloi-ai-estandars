# Runbook de conferencia: Codex + OLOI Plugins

Fecha sugerida: 2026-04-24
Duración sugerida: 25 a 35 minutos

## Estructura recomendada

### 0. Apertura - 2 min

Mensaje:

> Hoy no vengo a mostrar prompts sueltos. Vengo a mostrar cómo empaquetamos expertise interna en plugins privados de Codex para que el equipo trabaje con más consistencia, velocidad y control.

## 1. Problema actual - 3 min

Explica esto:

- cada developer resuelve tareas con estilos distintos,
- el conocimiento senior queda disperso,
- repetir buenas prácticas manualmente no escala,
- necesitamos estandarizar sin convertir el proceso en burocracia.

## 2. Solución - 4 min

Explica que `oloi-ai-estandars` ahora es:

- catálogo multi-dominio,
- marketplace privado de plugins,
- gobernanza por versiones y canales,
- base para escalar a nuevos dominios.

## 3. Mostrar arquitectura - 5 min

Abre en el repo:

- `/.agents/plugins/marketplace.json`
- `/plugins/oloi-laravel/.codex-plugin/plugin.json`
- `/plugins/oloi-flutter/.codex-plugin/plugin.json`
- `/plugins/oloi-product-analyst/.codex-plugin/plugin.json`

Mensaje clave:

- dominio fuente != paquete distribuible,
- la separación permite evolucionar y publicar sin romper el catálogo.

## 4. Demo de instalación - 5 min

Muestra:

1. abrir Codex en macOS,
2. abrir el repo,
3. ver el marketplace `OLOI Enterprise Plugins`,
4. instalar un plugin.

## 5. Demo funcional - 8 a 10 min

### Demo A: Laravel

Prompt:

`Use OLOI Laravel to create a production-safe endpoint for updating the authenticated user profile.`

### Demo B: Flutter

Prompt:

`Use OLOI Flutter to plan and implement a profile edit feature with Riverpod, Dio and tests.`

### Demo C: Product Analyst

Prompt:

`Use OLOI Product Analyst to convert this requirement into a sprint-ready user story and implementation tasks.`

## 6. Gobernanza - 4 min

Explica:

- `main`, `candidate`, `stable`,
- manifests y changelogs,
- tags por dominio,
- política interna y uso solo empresa.

## 7. Cierre - 2 min

Mensaje recomendado:

> El cambio real no es que ahora tenemos IA. El cambio real es que ahora podemos distribuir criterios técnicos y operativos como producto interno.

## 8. Preguntas esperables

### ¿Esto reemplaza seniors?

Respuesta:

No. Captura y distribuye parte del criterio senior, pero la revisión técnica y la decisión de arquitectura siguen siendo humanas.

### ¿Se puede extender a otros stacks?

Respuesta:

Sí. La estructura actual ya está lista para Next.js, Django, Design, QA o cualquier otro dominio.

### ¿Qué pasa si un plugin queda desactualizado?

Respuesta:

Se actualiza en el dominio fuente, se promueve por `candidate`, luego `stable`, y se publica una nueva versión por dominio.

### ¿Cómo controlamos uso fuera de la empresa?

Respuesta:

Con repo privado, permisos corporativos, marketplace interno, licencia propietaria y política de uso interno. La licencia sola no basta; por eso el control es legal + técnico + operativo.
