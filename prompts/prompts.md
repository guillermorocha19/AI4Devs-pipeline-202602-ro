# Histórico de Prompts

<!--
INSTRUCCIONES PARA EL AGENTE
============================
Al añadir nuevos prompts a este archivo:

1. Identifica la sesión activa (## Sesión: ...) o crea una nueva si cambia el tema/fecha.
2. Incrementa el ID del último prompt de esa sesión (P-001 → P-002 → P-003...).
3. Copia el prompt del usuario de forma literal, sin resumir ni parafrasear.
4. Rellena los metadatos: Tema, Fase (si aplica) y Contexto breve (1 línea).
5. Inserta la nueva entrada AL FINAL de la sesión, manteniendo el separador --- entre entradas.
6. NO incluyas respuestas del asistente, solo prompts del usuario.

Plantilla de entrada:
---

### P-XXX | <título corto descriptivo>
- **Tema:** <área: pipeline | ec2 | pr | docs | ...>
- **Fase:** <opcional: análisis | implementación | debug | ...>
- **Contexto:** <por qué se envió este prompt>

\`\`\`text
<prompt literal del usuario>
\`\`\`
-->

## Sesión: 2026-06-07 — Pipeline CI/CD Backend (GitHub Actions + EC2)

---

### P-001 | Análisis Fase 1 y diseño del pipeline
- **Tema:** pipeline
- **Fase:** análisis
- **Contexto:** Petición inicial del ejercicio DevOps en dos fases (análisis + implementación tras confirmación).

```text
Eres un experto DevOps Engineer especializado en GitHub Actions y despliegues en AWS. Tu tarea es analizar este proyecto con especial atención a la arquitectura y configuración del backend, y posteriormente diseñar un pipeline de CI/CD en GitHub Actions.

**FASE 1 — Análisis y confirmación**

Antes de escribir ningún código, realiza los siguientes pasos:

1. Examina la estructura completa del proyecto, con foco especial en el backend: lenguaje y framework utilizado, gestor de dependencias, scripts disponibles (build, test, start), variables de entorno necesarias, y cualquier archivo de configuración relevante (Dockerfile, docker-compose, configuraciones de base de datos, etc.).
2. Identifica los comandos exactos para instalar dependencias, ejecutar tests y generar el build del backend.
3. Resume todo lo que has entendido sobre el proyecto y espera mi confirmación explícita antes de continuar con la Fase 2.

**FASE 2 — Creación del pipeline (solo tras confirmación)**

Una vez confirmado el análisis, crea un archivo `.github/workflows/backend-ci-cd.yml` que implemente el siguiente flujo:

**Trigger:** `push` sobre cualquier rama que tenga un Pull Request abierto (usando el evento `pull_request` con los tipos `opened`, `synchronize` y `reopened`).

**Jobs a implementar** (en orden secuencial, con dependencias explícitas entre ellos):

- **Job 1 — Test:** Instala dependencias y ejecuta la suite de tests del backend. El pipeline debe detenerse si los tests fallan.
- **Job 2 — Build:** Solo se ejecuta si el Job 1 tiene éxito. Genera el artefacto de build del backend y lo persiste entre jobs usando `actions/upload-artifact`.
- **Job 3 — Deploy a EC2:** Solo se ejecuta si el Job 2 tiene éxito. Descarga el artefacto generado y lo despliega en una instancia EC2 de AWS mediante SSH (usando secrets de GitHub para credenciales: clave SSH, host, usuario y cualquier variable de entorno sensible).

**Requisitos adicionales del pipeline:**

- Usa versiones fijas y actuales de todas las actions (evita `@master` o `@latest`).
- Parametriza con `env` a nivel de workflow las variables no sensibles (región AWS, nombre de la app, directorio de despliegue en EC2, etc.).
- Incluye un paso de notificación o resumen final del resultado del despliegue.
- Añade comentarios explicativos en el YAML para facilitar el mantenimiento.
- Contempla y documenta qué secrets de GitHub deben configurarse en el repositorio para que el pipeline funcione.

Proporciona el archivo YAML completo y, a continuación, una breve explicación de las decisiones de diseño más importantes que hayas tomado.
```

---

### P-002 | Aclaración sobre PostgreSQL en EC2
- **Tema:** pipeline
- **Fase:** análisis
- **Contexto:** Pregunta sobre si el requisito de PostgreSQL en runtime afectará al workflow.

```text
¿Por qué indicas esto? Para EC2 (runtime): sí se necesita PostgreSQL accesible y un .env con DATABASE_URL válida. ¿Crees que el workflow solicitado fallará en algún paso por ello?
```

---

### P-003 | Confirmación para Fase 2
- **Tema:** pipeline
- **Fase:** implementación
- **Contexto:** Aprobación del análisis para crear el workflow.

```text
Ok, continua
```

---

### P-004 | Renombrar secret EC2_HOST → EC2_INSTANCE
- **Tema:** pipeline
- **Fase:** implementación
- **Contexto:** Alinear el nombre del secret con el README del proyecto.

```text
cambia EC2_HOST por EC2_INSTANCE
```

---

### P-005 | Ejecutar prerrequisitos
- **Tema:** ec2
- **Fase:** setup
- **Contexto:** Primera petición de preparar el entorno (sin especificar aún que sea solo EC2).

```text
Ejecuta los prerrequisitos
```

---

### P-006 | Prerrequisitos solo en EC2
- **Tema:** ec2
- **Fase:** setup
- **Contexto:** Restricción explícita: sin comprobaciones locales, solo contra EC2.

```text
Ejecuta los prerrequisitos contra EC2 directamente, si necesitas credenciales, yo te indico rutas. No hagas ninguna comprobación local. Vamos contra EC2 directamente
```

---

### P-007 | Credenciales y datos de la instancia EC2
- **Tema:** ec2
- **Fase:** setup
- **Contexto:** Entrega de credenciales SSH y metadatos de la instancia para el setup.

```text
1 @c:\Users\GuillermoRochaGarrid\Downloads\ai-monitoring.pem 2. ec2-13-51-56-66.eu-north-1.compute.amazonaws.com 3. ec2-user . 4 No necesario. 5. Linux/UNIX (amazon/al2023-ami-2023.11.20260526.0-kernel-6.1-x86_64)
```

---

### P-008 | Primera prueba: crear PR
- **Tema:** pr
- **Fase:** validación
- **Contexto:** Lanzar la primera ejecución del pipeline mediante Pull Request.

```text
Vamos con una primera prueba, haz una PR
```

---

### P-009 | Fix error ci.yml vacío
- **Tema:** pipeline
- **Fase:** debug
- **Contexto:** Reporte de fallo en GitHub Actions por workflow sin triggers.

```text
Te lo digo yo, ha fallado: "Error
No event triggers defined in `on`"
```

---

### P-010 | Corregir 4 warnings del workflow
- **Tema:** pipeline
- **Fase:** debug
- **Contexto:** Pipeline funcional pero con warnings de deprecación Node.js 20 y input inválido en ssh-action.

```text
Ha ido bien, pero tenemos 4 warnings. Vamos a arreglarlos. Te los pongo "Annotations
4 warnings
Test
Node.js 20 actions are deprecated. The following actions are running on Node.js 20 and may not work as expected: actions/checkout@v4.2.2, actions/setup-node@v4.2.0. Actions will be forced to run with Node.js 24 by default starting June 16th, 2026. Node.js 20 will be removed from the runner on September 16th, 2026. Please check if updated versions of these actions are available that support Node.js 24. To opt into Node.js 24 now, set the FORCE_JAVASCRIPT_ACTIONS_TO_NODE24=true environment variable on the runner or in your workflow file. Once Node.js 24 becomes the default, you can temporarily opt out by setting ACTIONS_ALLOW_USE_UNSECURE_NODE_VERSION=true. For more information see: https://github.blog/changelog/2025-09-19-deprecation-of-node-20-on-github-actions-runners/
Build
Node.js 20 actions are deprecated. The following actions are running on Node.js 20 and may not work as expected: actions/checkout@v4.2.2, actions/setup-node@v4.2.0, actions/upload-artifact@v4.6.0. Actions will be forced to run with Node.js 24 by default starting June 16th, 2026. Node.js 20 will be removed from the runner on September 16th, 2026. Please check if updated versions of these actions are available that support Node.js 24. To opt into Node.js 24 now, set the FORCE_JAVASCRIPT_ACTIONS_TO_NODE24=true environment variable on the runner or in your workflow file. Once Node.js 24 becomes the default, you can temporarily opt out by setting ACTIONS_ALLOW_USE_UNSECURE_NODE_VERSION=true. For more information see: https://github.blog/changelog/2025-09-19-deprecation-of-node-20-on-github-actions-runners/
Deploy to EC2
Node.js 20 actions are deprecated. The following actions are running on Node.js 20 and may not work as expected: actions/download-artifact@v4.1.9. Actions will be forced to run with Node.js 24 by default starting June 16th, 2026. Node.js 20 will be removed from the runner on September 16th, 2026. Please check if updated versions of these actions are available that support Node.js 24. To opt into Node.js 24 now, set the FORCE_JAVASCRIPT_ACTIONS_TO_NODE24=true environment variable on the runner or in your workflow file. Once Node.js 24 becomes the default, you can temporarily opt out by setting ACTIONS_ALLOW_USE_UNSECURE_NODE_VERSION=true. For more information see: https://github.blog/changelog/2025-09-19-deprecation-of-node-20-on-github-actions-runners/
Deploy to EC2
Unexpected input(s) 'script_stop', valid inputs are ['host', 'port', 'passphrase', 'username', 'password', 'protocol', 'sync', 'use_insecure_cipher', 'cipher', 'timeout', 'command_timeout', 'key', 'key_path', 'fingerprint', 'proxy_host', 'proxy_port', 'proxy_username', 'proxy_password', 'proxy_protocol', 'proxy_passphrase', 'proxy_timeout', 'proxy_key', 'proxy_key_path', 'proxy_fingerprint', 'proxy_cipher', 'proxy_use_insecure_cipher', 'script', 'script_path', 'envs', 'envs_format', 'debug', 'allenvs', 'request_pty', 'curl_insecure', 'capture_stdout', 'version']
"
```

---

### P-011 | Volcar histórico de prompts
- **Tema:** docs
- **Fase:** documentación
- **Contexto:** Crear registro de prompts de la sesión para reutilización futura por agentes.

```text
Vuelca en @prompts/prompts.md todos los prompts utilizados en esta sesión (sólo los mios) para tener un histórico de prompts usados. Usa una estructura que posteriormente se pueda entender muy rápido por un agente para añadir más prompts
```
