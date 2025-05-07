# Wiki.js con Docker, Nginx y Certbot

Este repositorio contiene la configuración necesaria para desplegar una instancia de Wiki.js utilizando Docker Compose, junto con Nginx como servidor proxy inverso y PostgreSQL como base de datos.

## ¿Qué es Wiki.js?
Wiki.js es una potente plataforma wiki de código abierto, moderna y elegante, desarrollada con Node.js. Permite la creación y gestión colaborativa de documentación, bases de conocimiento y contenido estructurado.

## Acerca de este proyecto
Este repositorio proporciona una configuración Docker completa para desplegar Wiki.js en un entorno de producción con:

- **Wiki.js**: La aplicación principal para gestionar tu contenido wiki
- **Nginx**: Servidor web que actúa como proxy inverso, mejorando la seguridad y rendimiento
- **PostgreSQL**: Base de datos robusta para almacenar todo el contenido
- **Certbot**: Gestión automatizada de certificados SSL para garantizar conexiones seguras

Esta configuración permite desplegar rápidamente una plataforma de documentación colaborativa completa, segura y escalable para equipos, organizaciones o proyectos de cualquier tamaño.
## Estructura del proyecto

La configuración incluye los siguientes servicios:

- **nginx**: Servidor web que actúa como proxy inverso para Wiki.js
- **wikijs**: La aplicación principal Wiki.js
- **wikijs-db**: Base de datos PostgreSQL para almacenar el contenido de Wiki.js
- **certbot**: Servicio opcional para la gestión de certificados SSL

## Requisitos previos

- Un servidor con acceso público a internet (para certificados SSL)
- Puertos 80 y 443 disponibles

## Instalación

1. Clona este repositorio:
```bash
git clone https://github.com/pederysky/wiki.js-nginx-certbot.git
cd wiki.js-nginx-certbot
```

2. Crea las carpetas necesarias para los volúmenes persistentes:
```bash
mkdir -p data certbot/etc certbot/lib certbot/log nginx
```

3. Inicia los servicios:
```bash
docker-compose up -d
```

## Acceso a Wiki.js

Una vez que los contenedores estén en funcionamiento, puedes acceder a Wiki.js a través de:

- http://localhost:3000 (si accedes desde el servidor)
- http://tu-dominio.com (si has configurado un dominio)

En el primer acceso, se te pedirá completar la configuración inicial de Wiki.js.

## Configuración de SSL

El archivo docker-compose incluye un servicio Certbot para gestionar certificados SSL. Para configurarlo correctamente:

1. Detén los servicios:
```bash
docker-compose down
```

2. Actualiza la configuración de Nginx para incluir tu dominio en lugar de "localhost"

3. Configura Certbot para obtener certificados para tu dominio:
```bash
docker-compose run --rm certbot certonly --webroot --webroot-path=/var/www/certbot -d tu-dominio.com -d www.tu-dominio.com
```

4. Actualiza nuevamente la configuración de Nginx para usar los certificados SSL

5. Reinicia los servicios:
```bash
docker-compose up -d
```

## Personalizaciones

### Cambio de credenciales de la base de datos

Para mayor seguridad, se recomienda cambiar las credenciales predeterminadas de la base de datos. Modifica las siguientes variables en el archivo docker-compose.yml:

```yaml
# En el servicio wikijs-db
environment:
  - POSTGRES_USER=tu_usuario
  - POSTGRES_PASSWORD=tu_contraseña_segura
  - POSTGRES_DB=wikijs

# En el servicio wikijs (para que coincidan)
environment:
  - DB_USER=tu_usuario
  - DB_PASS=tu_contraseña_segura
```

### Personalización de Nginx

Si necesitas personalizar la configuración de Nginx, puedes modificar el comando en el servicio de Nginx para que apunte a un archivo de configuración personalizado montado como volumen.

## Respaldo de datos

La base de datos PostgreSQL guarda sus datos en el directorio `./data`. Para realizar copias de seguridad:

1. Detén los servicios o asegúrate de que no hay escrituras:
```bash
docker-compose pause wikijs wikijs-db
```

2. Haz una copia de seguridad del directorio de datos:
```bash
tar -czf wikijs-backup-$(date +%Y%m%d).tar.gz data/
```

3. Reanuda los servicios:
```bash
docker-compose unpause wikijs wikijs-db
```

## Solución de problemas

Si encuentras problemas con la implementación, comprueba los logs de los servicios:

```bash
# Ver logs de todos los servicios
docker-compose logs

# Ver logs de un servicio específico
docker-compose logs wikijs
docker-compose logs nginx
docker-compose logs wikijs-db
```
