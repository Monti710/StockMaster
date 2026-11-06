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

El repositorio contiene código de una aplicación existente, pero no incluye `requirements.txt` ni un archivo de bloqueo de dependencias. Las migraciones indican Django 3.2.23 y la cabecera de configuración menciona Django 4.2.4.

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
python -m pip install "Django>=4.2,<5.0" mysqlclient Pillow numpy pytz face-recognition
```

Esta lista se deriva de la configuración y los imports del proyecto. `face-recognition` depende de `dlib`, cuya instalación puede requerir CMake y un compilador C++. Windows no cuenta con soporte oficial de esa biblioteca; consulta sus [instrucciones de instalación](https://github.com/ageitgey/face_recognition#installation).

El módulo facial se importa al cargar las vistas, por lo que sus dependencias también son necesarias para iniciar el proyecto tal como está escrito, aunque se utilice el acceso con contraseña.

### 4. Crear la base de datos

Con MySQL en ejecución, crea una base vacía desde su cliente:

```sql
CREATE DATABASE stockmaster CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

Configura `DATABASES` en `StockMaster/settings.py` con los datos de tu instalación:

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'stockmaster',
        'USER': 'tu_usuario_mysql',
        'PASSWORD': 'tu_password_mysql',
        'HOST': '127.0.0.1',
        'PORT': '3306',
    }
}
```

El proyecto configura estos valores directamente en Python; no incluye carga automática de `.env`. No publiques credenciales reales en el repositorio.

### 5. Ajustar la configuración local

En `StockMaster/settings.py`:

- Sustituye `SECRET_KEY` por una clave propia de tu entorno.
- Limita `ALLOWED_HOSTS` a `['localhost', '127.0.0.1']` para la prueba local.
- La configuración de `STATICFILES_DIRS` apunta a una carpeta `static/` en la raíz que no está incluida. Créala si la necesitas o retira esa entrada; los recursos existentes están en `StockMaster_app/static/`.

Para generar una clave de Django:

```bash
python -c "from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())"
```

Para probar las funciones de correo sin enviar mensajes reales, utiliza:

```python
EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'
```

Los correos aparecerán en la terminal. Para envío real, configura un servicio SMTP propio y revisa también los remitentes definidos en `StockMaster_app/views.py`.

### 6. Aplicar migraciones y crear un administrador

```bash
python manage.py check
python manage.py migrate
python manage.py createsuperuser
```

Utiliza una base vacía para este procedimiento. Los archivos `stockmaster.sql` y `stockmaster2.sql` son respaldos existentes y no son necesarios para crear las tablas desde las migraciones. No los importes sobre esta instalación sin revisar su esquema y contenido.

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

Para probarlo, prepara perfiles con fotografías válidas y accesibles, permite el uso de la cámara en el navegador y verifica la configuración de almacenamiento de archivos. `MEDIA_ROOT` y `MEDIA_URL` no están definidos en la configuración incluida; deben ajustarse si se desea controlar la ubicación y publicación de las imágenes.

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
├── logs/                      # Modelos de registros y capturas existentes
├── stockmaster.sql            # Respaldo de base de datos
├── stockmaster2.sql           # Respaldo alternativo
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
- La configuración actual contiene valores sensibles escritos directamente en el código. Deben sustituirse y, si se han compartido, rotarse antes de un uso real.
- Revisa y anonimiza los respaldos SQL y las capturas antes de distribuirlos.
- Añade reglas de exclusión para entornos virtuales, secretos, archivos `__pycache__`, respaldos privados y archivos cargados por usuarios.
- Para producción, desactiva `DEBUG`, restringe los hosts y configura un servidor de aplicaciones; `runserver` se utiliza aquí únicamente para desarrollo local.

## Equipo

- [@mpegueros](https://github.com/mpegueros) – UI/UX Designer, Front-End Developer & Documentation Lead
- [@Gavalencia12](https://github.com/Gavalencia12) – UI/UX Designer, Front-End Developer & Documentation Lead
- [@Angel1591Diaz](https://github.com/Angel591Diaz) – Back-End Developer & Database Contributor
- [@Monti710](https://github.com/Monti710) – Full-Stack Developer & Database Contributor

## Contribuciones y licencia

Las propuestas de mejora pueden presentarse mediante issues y pull requests en este repositorio. Describe el cambio y los pasos utilizados para comprobarlo.

El repositorio no incluye un archivo de licencia. Consulta con sus autores las condiciones de uso y redistribución.
