# StockMaster

**Gestión de inventario, proveedores y pedidos desde una aplicación web.**

StockMaster centraliza el registro de productos, el control de existencias y la administración de proveedores. Incluye organización por categorías, marcas y áreas, gestión de usuarios y roles, historial de operaciones y un módulo de acceso mediante reconocimiento facial.

## Funcionalidades

- **Productos e inventario:** registro, edición, búsqueda, imágenes y actualización de cantidades.
- **Proveedores:** administración de contactos, direcciones y datos comerciales.
- **Organización:** clasificación por categorías, marcas y áreas.
- **Pedidos:** registro de órdenes, cantidades, importes, entrega y notificaciones por correo.
- **Usuarios y roles:** autenticación con Django y permisos para las distintas secciones.
- **Bajas y recuperación:** desactivación y recuperación de registros, con opciones de eliminación definitiva.
- **Historial:** consulta de modificaciones, movimientos y eliminaciones.
- **Panel de actividad:** indicadores y gráficas sobre el inventario.
- **Soporte interno:** envío de comentarios y respuestas.
- **Reconocimiento facial:** comparación de capturas con fotografías de los perfiles registrados.

## Tecnologías

| Área | Tecnologías |
| --- | --- |
| Backend | Python y Django |
| Base de datos | MySQL y ORM de Django |
| Frontend | Plantillas Django, HTML, CSS y JavaScript |
| Interfaz y gráficas | Bootstrap, jQuery, Font Awesome y Chart.js |
| Reconocimiento facial | face_recognition, dlib y NumPy |
| Imágenes | Pillow y campos de imagen de Django |
| Correo | Backend SMTP de Django |

## Estado y compatibilidad

El repositorio contiene código de una aplicación existente, con dependencias declaradas en `requirements.txt`, sin un archivo de bloqueo de versiones completas. Las migraciones indican Django 3.2.23 y la cabecera de configuración menciona Django 4.2.4.

La instalación siguiente sirve como punto de partida para reproducir el proyecto localmente; no representa una combinación de versiones validada mediante ejecución. Se utiliza Django 4.2 para conservar compatibilidad con las migraciones actuales: estas importan `django.utils.timezone.utc`, un alias eliminado en Django 5.0. Consulta el [calendario de cambios de Django](https://docs.djangoproject.com/en/5.0/internals/deprecation/).

Antes de desplegar la aplicación, actualiza y valida sus dependencias con una versión de Django que tenga soporte vigente.

## Instalación local

### 1. Clonar el repositorio

```bash
git clone https://github.com/Monti710/StockMaster.git
cd StockMaster
```

Ejecuta los comandos desde esta carpeta, donde se encuentra el `manage.py` principal.

### 2. Crear un entorno virtual

Como punto de partida para este código histórico, utiliza Python 3.10 o 3.11.

```bash
python -m venv .venv
```

Actívalo según tu terminal:

```bash
# Windows: Git Bash
source .venv/Scripts/activate

# Windows: PowerShell
.venv\Scripts\Activate.ps1

# Linux o macOS
source .venv/bin/activate
```

### 3. Instalar dependencias

```bash
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

Esta lista se deriva de la configuración y los imports del proyecto. `face-recognition` depende de `dlib`, cuya instalación puede requerir CMake y un compilador C++. Windows no cuenta con soporte oficial de esa biblioteca; consulta sus [instrucciones de instalación](https://github.com/ageitgey/face_recognition#installation).

El módulo facial se importa al cargar las vistas, por lo que sus dependencias también son necesarias para iniciar el proyecto tal como está escrito, aunque se utilice el acceso con contraseña.

### 4. Crear la base de datos

Con MySQL en ejecución, crea una base vacía desde su cliente:

```sql
CREATE DATABASE stockmaster CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

### 5. Configurar el entorno local

Copia `.env.example` como `.env` y configura `DB_NAME`, `DB_USER`, `DB_PASSWORD`, `DB_HOST` y `DB_PORT` para una cuenta MySQL propia. El archivo `.env` está excluido de Git; nunca lo publiques.

```bash
# Git Bash, Linux o macOS
cp .env.example .env

# PowerShell (alternativa)
Copy-Item .env.example .env
```

Genera una clave nueva y coloca el resultado en `DJANGO_SECRET_KEY` dentro de `.env`:

```bash
python -c "import secrets; print(secrets.token_urlsafe(64))"
```

La aplicación exige una clave y se detiene si falta. Para desarrollo local establece `DJANGO_DEBUG=True`; conserva `False` en producción. `DJANGO_ALLOWED_HOSTS` admite una lista de hosts separados por comas.

El correo utiliza la consola de forma predeterminada. Para activar SMTP, configura `EMAIL_BACKEND=django.core.mail.backends.smtp.EmailBackend` junto con `EMAIL_HOST`, `EMAIL_PORT`, `EMAIL_USE_TLS`, `EMAIL_HOST_USER`, una credencial nueva en `EMAIL_HOST_PASSWORD` y un `DEFAULT_FROM_EMAIL` autorizado. `SUPPORT_EMAIL` configura el destinatario del correo de prueba; ese endpoint requiere un superusuario y una solicitud POST.

Los valores del entorno del proceso tienen prioridad sobre `.env`. Los archivos subidos se guardan en `media/`, que también está excluido de Git. `STATICFILES_DIRS` solo incluye la carpeta raíz `static/` si existe.

### 6. Aplicar migraciones y crear un administrador

```bash
python manage.py check
python manage.py migrate
python manage.py createsuperuser
```

Utiliza una base vacía para este procedimiento. Los respaldos con datos de usuarios se retiraron del repositorio; las tablas se crean con las migraciones. Carga únicamente datos de prueba propios.

### 7. Iniciar el servidor

```bash
python manage.py runserver
```

Abre [http://127.0.0.1:8000/signin/](http://127.0.0.1:8000/signin/) e inicia sesión con el superusuario creado. El administrador de Django está en [http://127.0.0.1:8000/admin/](http://127.0.0.1:8000/admin/).

## Primeros pasos

1. Crea categorías, marcas y áreas para organizar los productos.
2. Registra los proveedores.
3. Agrega productos con sus relaciones y cantidades.
4. Configura roles y permisos antes de incorporar usuarios operativos.
5. Consulta el inventario, registra pedidos y revisa el historial de actividad.

## Reconocimiento facial

El acceso facial se encuentra en `/login/` y utiliza fotografías asociadas al modelo `Profile`. Las capturas se registran mediante el modelo `Log`.

Para probarlo, prepara perfiles con fotografías válidas y accesibles, permite el uso de la cámara en el navegador y verifica la configuración de almacenamiento de archivos. `MEDIA_ROOT` apunta a `media/` y `MEDIA_URL` a `/media/`. La publicación de imágenes debe configurarse según el entorno, con controles de acceso apropiados; definir estas opciones no publica los archivos automáticamente.

Este módulo requiere validación adicional antes de utilizarse como mecanismo de autenticación en producción. Usa imágenes de prueba con autorización de sus participantes.

## Estructura del proyecto

```text
StockMaster/
├── manage.py                  # Comandos de administración
├── StockMaster/               # Configuración, URLs, ASGI y WSGI
├── StockMaster_app/           # Lógica principal de inventario
│   ├── migrations/            # Migraciones de la aplicación
│   ├── static/                # Estilos, scripts e imágenes
│   ├── templates/             # Vistas HTML
│   ├── templatetags/           # Filtros de plantillas
│   ├── models.py              # Modelos de datos
│   ├── urls.py                # Rutas
│   ├── utils.py               # Utilidades de reconocimiento facial
│   └── views.py               # Control de solicitudes
├── profiles/                  # Perfiles y fotografías de usuarios
├── logs/                      # Modelos y migraciones de registros
├── .env.example               # Plantilla sin secretos
├── .gitignore                 # Exclusiones de archivos privados
├── requirements.txt           # Dependencias del proyecto
├── SECURITY.md                # Rotación y limpieza tras exposición
└── README.md
```

## Rutas principales

| Ruta | Sección |
| --- | --- |
| `/signin/` | Acceso con usuario y contraseña |
| `/actividades/` | Panel de actividad |
| `/inventario/` | Inventario |
| `/productos/` | Productos |
| `/prov/` | Proveedores |
| `/config/` | Configuración de categorías y marcas |
| `/area/` | Áreas |
| `/ordenes/` | Pedidos |
| `/usuarios/` | Usuarios |
| `/rol/` | Roles |
| `/historial/` | Historial general |
| `/soporte/` | Soporte |
| `/login/` | Acceso facial |
| `/admin/` | Administración de Django |

El acceso a las secciones depende de los permisos asignados al usuario.

## Consideraciones del repositorio

- Los archivos `tests.py` incluidos son plantillas y no contienen pruebas funcionales implementadas.
- La configuración utiliza variables de entorno o un `.env` local excluido de Git.
- Los respaldos SQL, las capturas de acceso y los archivos compilados se excluyen del repositorio.
- Si utilizaste una copia anterior con credenciales incluidas, sigue [SECURITY.md](SECURITY.md): limpiar Git no revoca credenciales ni elimina copias ajenas.
- Para producción, desactiva `DEBUG`, restringe los hosts y configura un servidor de aplicaciones; `runserver` se utiliza aquí únicamente para desarrollo local.

## Equipo

- [@mpegueros](https://github.com/mpegueros) – UI/UX Designer, Front-End Developer & Documentation Lead
- [@Gavalencia12](https://github.com/Gavalencia12) – UI/UX Designer, Front-End Developer & Documentation Lead
- [@Angel1591Diaz](https://github.com/Angel591Diaz) – Back-End Developer & Database Contributor
- [@Monti710](https://github.com/Monti710) – Full-Stack Developer & Database Contributor

## Contribuciones y licencia

Las propuestas de mejora pueden presentarse mediante issues y pull requests en este repositorio. Describe el cambio y los pasos utilizados para comprobarlo.

El repositorio no incluye un archivo de licencia. Consulta con sus autores las condiciones de uso y redistribución.
