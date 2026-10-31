# NGINX CLI Server Manager

Script para gestionar servidores virtuales en NGINX desde la terminal.

## Descripción

Este proyecto permite crear, habilitar, deshabilitar y eliminar configuraciones de sitios web en NGINX de forma automática. Está pensado para entornos locales o de laboratorio donde se necesita crear servidores virtuales rápidamente sin depender de un panel visual.

La herramienta trabaja con archivos de configuración en `/etc/nginx/sites-available`, crea directorios web en `/var/www/html`, y puede gestionar tanto sitios estáticos como instalaciones de Joomla con base de datos MySQL.

## Qué hace realmente

El repositorio incluye dos versiones del script:

- `servers`: versión original, con gestión básica de sitios NGINX y soporte para Joomla.
- `servers-v2`: versión mejorada, con validación de entrada, comprobación de puertos, manejo más seguro de errores y estructura más ordenada.

## Funcionalidades

- Crear servidores virtuales de NGINX
- Configurar sitios con `listen` y `server_name`
- Crear directorios web para cada dominio o nombre de servidor
- Habilitar y deshabilitar sitios en `sites-enabled`
- Eliminar servidores y limpiar configuración asociada
- Listar servidores existentes con puerto, estado y directorio web
- Soporte para sitios HTML estáticos
- Soporte para Joomla con:
  - descarga e instalación automática
  - creación de base de datos MySQL
  - generación de credenciales de acceso
  - configuración de permisos para PHP/FPM
- Validación de nombre de servidor y puertos
- Detección de conflictos de puertos usando `ss`, `lsof` y Docker

## Requisitos

Antes de usarlo, asegúrate de tener:

- Linux o un sistema tipo Unix
- NGINX instalado
- MySQL/MariaDB si vas a crear sitios Joomla
- PHP-FPM para instalaciones de Joomla
- Permisos de administrador o `sudo`
- Acceso a la terminal

## Uso

### Ver ayuda

```bash
./servers -h
```

```bash
./servers-v2 -h
```

### Listar servidores

```bash
./servers
```

```bash
./servers-v2 -l
```

### Crear un nuevo servidor

```bash
./servers -n
```

```bash
./servers-v2 -n
```

El script te pedirá:

- nombre del servidor
- puerto
- tipo de servidor:
  - simple (HTML estático)
  - Joomla (PHP + MySQL)

### Habilitar un servidor

```bash
./servers -e ejemplo.local
./servers-v2 -e ejemplo.local
```

### Deshabilitar un servidor

```bash
./servers -d ejemplo.local
./servers-v2 -d ejemplo.local
```

### Eliminar un servidor

```bash
./servers -rm ejemplo.local
./servers-v2 -rm ejemplo.local
```

## Estructura que crea el script

Los sitios se generan normalmente así:

- Configuración NGINX: `/etc/nginx/sites-available/<nombre>`
- Enlace habilitado: `/etc/nginx/sites-enabled/<nombre>`
- Directorio web: `/var/www/html/<nombre>`

## Tipo de servidor: simple

Crea un sitio estático con una plantilla HTML básica y un bloque NGINX similar a este:

```nginx
server {
    listen 8003;
    server_name ejemplo.local;

    root /var/www/html/ejemplo.local;
    index index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

## Tipo de servidor: Joomla

Si eliges Joomla, el script puede:

- crear una base de datos en MySQL
- crear el usuario correspondiente
- generar credenciales en `/var/www/html/<nombre>/db_credentials.txt`
- descargar Joomla
- preparar permisos para PHP y NGINX
- dejarte listo para completar la instalación desde el navegador

## Recomendaciones

- Reinicia NGINX después de habilitar o eliminar sitios:

```bash
sudo systemctl restart nginx
```

- Guarda las credenciales de Joomla en un lugar seguro y elimínalas después de la instalación.
- Usa nombres de servidor consistentes y puertos libres.
- Si se usa un entorno con Docker, revisa si el puerto está ocupado por un contenedor.

## Notas

Este proyecto está orientado a administración local y automatización de despliegues simples. No reemplaza un entorno de producción completo, pero sí facilita la creación y gestión rápida de sitios web en un servidor Linux con NGINX.

## Licencia

Este proyecto se distribuye bajo la licencia MIT.
