# Configuración y respuesta a exposición

Las credenciales se cargan desde variables de entorno o un archivo `.env` local. `.env.example` contiene únicamente nombres de variables y valores de ejemplo. No copies claves antiguas ni las publiques en issues, capturas o commits.

## Acciones necesarias para instalaciones anteriores

1. Revoca la contraseña de aplicación SMTP expuesta desde la cuenta de Google que la creó. Si hace falta SMTP, crea una nueva y guárdala exclusivamente en el entorno privado del servidor.
2. Genera una `DJANGO_SECRET_KEY` nueva en cada despliegue. No conserves la clave anterior como fallback.
3. Invalida todas las sesiones de la base activa. Con el entorno correcto y las dependencias instaladas, ejecuta:

```bash
python manage.py shell -c "from django.contrib.sessions.models import Session; Session.objects.all().delete()"
```

Este comando cierra todas las sesiones de esa instalación. Borrar los respaldos del repositorio no ejecuta esta operación en una base de datos activa.

4. Los respaldos expuestos incluían hashes de contraseñas. Solicita el cambio de contraseña de las cuentas afectadas y revisa los accesos a la cuenta de correo y al sistema.
5. Tras reescribir el historial, los colaboradores deben volver a clonar el repositorio. No fusionen ni vuelvan a subir ramas con el historial anterior. Conserven cambios locales legítimos por separado y revísenlos antes de aplicarlos a la copia limpia.
6. Las copias, forks y vistas de commits antiguos pueden conservar datos. Para los respaldos y capturas personales, solicita a GitHub Support la eliminación de referencias o vistas almacenadas que sigan accesibles, indicando los commits anteriores y los archivos afectados sin adjuntar sus contenidos.

## Alcance de la limpieza

Se retiran del historial los respaldos SQL, los archivos Python compilados y las capturas de acceso de `logs/`. Se sustituyen los secretos y correos incrustados en archivos de texto. El código, las migraciones, los recursos de interfaz y los créditos se conservan.

La limpieza del repositorio no equivale a una auditoría completa de seguridad ni confirma la rotación de servicios externos. La versión histórica de Django y el módulo de autenticación facial necesitan revisión antes de producción.

Referencia: [Eliminar información sensible de un repositorio](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository).
