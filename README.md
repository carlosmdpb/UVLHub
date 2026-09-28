# Montaito-Hub — Evolución de UVLHub

Plataforma web para compartir, consultar y analizar modelos de características en formato UVL. Estos modelos describen las opciones y restricciones de una familia de productos software: qué funcionalidades pueden elegirse y cuáles son compatibles.

Montaito-Hub es un fork académico de UVLHub, desarrollado en equipo para Evolución y Gestión de la Configuración (EGC), Universidad de Sevilla, curso 2024/25. Amplía la plataforma con valoraciones, perfiles, estadísticas y servicios de integración, junto con herramientas de pruebas y automatización.

## Funcionalidades

| Área | Qué permite |
| --- | --- |
| Datasets y modelos | Subir, consultar, buscar y descargar modelos UVL agrupados en datasets |
| Análisis | Integración con Flamapy para operaciones sobre modelos de características |
| Publicación | Integración con Zenodo para compartir recursos de investigación |
| Comunidad | Valorar datasets y modelos, consultar perfiles y rankings |
| Dashboard | Consultar estadísticas de actividad |
| Autenticación | Registro, inicio de sesión y recuperación de contraseña por correo |
| Discord | Bot de consulta conectado a la plataforma |
| Asistente | Integración con Google Dialogflow |
| Pruebas de integración | Fakenodo, simulación local de operaciones de Zenodo |

Las conexiones con Zenodo, Discord, Dialogflow y correo requieren su propia configuración. Fakenodo devuelve respuestas simuladas; no es un detector de datos falsos.

## Tecnologías y arquitectura

- Python 3.12 y Flask 3.0.3.
- SQLAlchemy, Flask-Migrate y MariaDB.
- Plantillas Jinja y recursos HTML, CSS y JavaScript.
- Flamapy para modelos de características.
- Docker Compose y Nginx.
- CLI propia Rosemary para comandos de desarrollo.
- Pytest, Selenium, Locust y configuración de GitHub Actions.

El backend se organiza por módulos en `app/modules/`, con rutas, servicios, repositorios, modelos y pruebas. `core/` contiene las clases y gestores compartidos. La aplicación registra los módulos desde su factoría Flask.

## Ejecutar con Docker

Requisitos: Git, Docker y Docker Compose con contenedores Linux. El entorno de desarrollo expone Nginx en el puerto 80 y MariaDB en el 3306; deben estar disponibles.

```sh
git clone https://github.com/carlosmdpb/UVLHub.git
cd UVLHub
```

Crear la configuración:

```powershell
# Windows / PowerShell
Copy-Item .env.docker.example .env
```

```sh
# Linux / macOS
cp .env.docker.example .env
```

Iniciar los servicios desde la raíz:

```sh
docker compose -f docker/docker-compose.dev.yml up --build -d
docker compose -f docker/docker-compose.dev.yml logs -f web
```

Acceso: [http://localhost](http://localhost). Nginx dirige las peticiones al servicio Flask del puerto interno 5000.

El entrypoint espera a MariaDB, crea la base de pruebas y aplica las migraciones; si la base está vacía, carga los seeders. La imagen instala las dependencias y Rosemary, por lo que este recorrido no necesita un entorno virtual Python en el equipo anfitrión.

Detener el entorno:

```sh
docker compose -f docker/docker-compose.dev.yml down
```

Ese comando conserva el volumen de datos. La configuración Compose monta el directorio del proyecto y el socket Docker en el contenedor web: es una configuración destinada a desarrollo local.

## Integraciones externas

- **Discord:** añadir `DISCORD_BOT_TOKEN` a la configuración privada para activar el bot. Sin token, el arranque omite su ejecución.
- **Dialogflow:** proporcionar un proyecto y credenciales Google compatibles con el cliente utilizado en `app/modules/ai/`.
- **Zenodo:** revisar la configuración del módulo `zenodo` antes de operaciones remotas; usar un entorno de pruebas.
- **Correo:** la recuperación de contraseña incluye configuración SMTP escrita en el código. Debe sustituirse y revocarse la credencial existente antes de publicar o probar ese flujo.

No añadir valores secretos al README ni versionar la configuración privada.

## Pruebas

Con los servicios iniciados, desde la raíz:

```sh
docker compose -f docker/docker-compose.dev.yml exec web rosemary test
docker compose -f docker/docker-compose.dev.yml exec web rosemary test dataset
```

Rosemary también incluye `selenium` y `locust`. Las pruebas de navegador requieren su entorno Selenium/Chrome; las de carga necesitan la aplicación accesible. Revisar las opciones con `rosemary selenium --help` y `rosemary locust --help` dentro del contenedor.

La carpeta `.github/workflows/` contiene configuración de pruebas, lint, validación de commits, análisis Trivy y despliegue. El workflow `run-tests.yml` excluye varios archivos de pruebas: no acredita que se ejecuten todos los flujos. Este README no afirma un resultado actual de CI ni un porcentaje de cobertura.

## Estructura y documentación

```text
app/modules/  Funcionalidades y pruebas
app/templates/ Plantillas compartidas
app/static/   Recursos del frontend
core/         Infraestructura común
rosemary/     CLI de desarrollo
docker/       Imágenes, Compose, Nginx y entrypoints
migrations/   Versiones del esquema
docs/         Memoria, acuerdos y diario del equipo
.github/      Automatización
```

- [Memoria del proyecto](docs/Project_report.md).
- [Organización del equipo](docs/Articles_of_incorporation.md).
- [Diario del equipo](docs/Team_diary.md).
- [Integrantes y contexto académico](docs/Involvement.md).
- [Arranque Flask](app/__init__.py).
- [Configuración Docker de desarrollo](docker/docker-compose.dev.yml).

Los documentos académicos reflejan el contexto de la entrega. Para los detalles de funcionamiento, debe prevalecer el código; por ejemplo, Fakenodo simula la API de Zenodo.

## Origen y alcance

El proyecto parte de [UVLHub de Diverso Lab](https://github.com/diverso-lab/uvlhub) y conserva sus módulos y atribuciones. Montaito-Hub es un trabajo de evolución de software realizado en equipo, con configuración de desarrollo y servicios externos que requieren preparación.

La descripción corresponde a esta copia del repositorio. No presupone que los despliegues históricos sigan disponibles.
