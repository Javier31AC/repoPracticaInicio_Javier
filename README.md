# Proyecto Base: Flask con Docker y Calidad Automática

Este proyecto establece un *stack* de desarrollo y despliegue moderno y robusto para una aplicación **Flask**, aprovechando **Docker**, **Docker Compose** y un *pipeline* de calidad de código automatizado con **Black** y **Pytest**.

## 1. Información General

| Componente | Descripción |
| :--- | :--- |
| **Aplicación** | Microservicio **Flask** con una ruta principal (`/`). |
| **Lenguaje Base** | Python **3.11-slim**. |
| **Calidad** | Controlada automáticamente por un *hook* **pre-commit**. |

### Entornos Definidos

El proyecto distingue claramente entre entornos de producción y desarrollo para optimizar recursos y la experiencia de desarrollo.

| Entorno | Imagen Base | Herramientas Clave | Propósito |
| :--- | :--- | :--- | :--- |
| **Producción** | `python:3.11-slim` | Flask | Despliegue final y ligero. |
| **Desarrollo** | `python:3.11-slim` | Flask, **Pytest**, **Black** | Pruebas, *debugging* y formateo. |

***

## 2. Cómo Levantar la Aplicación (Producción)

Utiliza el `Dockerfile` principal para construir la imagen de producción final y ligera.

### 2.1. Construir la Imagen

```bash
docker build -t mi-app:latest .
```
### 2.2. Ejecutar el Contenedor
El contenedor expone la aplicación en el puerto 8000.

```bash

docker run --rm -p 8000:8000 mi-app:latest
```
🔗 La aplicación estará accesible en --> http://localhost:8000.

## 3. Entorno de Desarrollo (Docker Compose)
Para mantener la consistencia en las versiones de Python y las dependencias de calidad, todos los comandos de desarrollo se ejecutan a través del servicio dev definido en docker-compose.yml.

### 3.1. Construir el Servicio de Desarrollo

```bash

docker compose build dev
```
### 3.2. Ejecutar Comandos en el Entorno
Usa docker compose run --rm -T dev seguido del comando que deseas ejecutar dentro del contenedor de desarrollo.



### Ejemplo: Verificar la versión de Python
```bash
docker compose run --rm -T dev python --version
```
## 4. Ejecutar Tests y Formateo (Calidad)
Asegúrate de ejecutar estos comandos a través del servicio dev para garantizar que usas la versión correcta de las herramientas.

### 4.1. Tests Unitarios (Pytest)
Ejecuta todas las pruebas definidas en el proyecto:

```bash

docker compose run --rm -T dev pytest
```
### 4.2. Formateo de Código (Black)
Verificar Formato (Modo Dry-Run)
Comprueba si hay errores de formato sin modificar ningún archivo.

```bash

docker compose run --rm -T dev black --check .
```
Autoformatear y Corregir
Aplica correcciones de formato directamente a tus archivos locales.

```bash

docker compose run --rm -T dev black .
```
## 5. Funcionamiento del Hook pre-commit
La piedra angular de la calidad en este proyecto es el hook de Git ubicado en .git/hooks/pre-commit.

Flujo de Control de Calidad Automático
Este script se ejecuta automáticamente antes de que cada git commit se complete, asegurando que solo el código limpio y funcional se suba al repositorio.

Formateo (Black): El hook primero intenta ejecutar black . para auto-corregir cualquier problema de formato en los archivos que has preparado (git add).

Tests (Pytest): A continuación, ejecuta pytest -q sobre el código ahora formateado.

Bloqueo: Si cualquier test falla, el script aborta el commit con un error (exit 1).

Éxito: Si el código está correctamente formateado y todos los tests pasan, el commit se completa satisfactoriamente.

NOTA: Asegúrate de que el hook tenga permisos de ejecución en tu sistema:

```bash

chmod +x .git/hooks/pre-commit
```
