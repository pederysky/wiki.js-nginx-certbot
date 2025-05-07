# Wiki.js con Docker, Nginx y Certbot
Este repositorio contiene la configuración necesaria para desplegar una instancia de Wiki.js utilizando Docker Compose, junto con Nginx como servidor proxy inverso y PostgreSQL como base de datos.
Estructura del proyecto
La configuración incluye los siguientes servicios:

nginx: Servidor web que actúa como proxy inverso para Wiki.js
wikijs: La aplicación principal Wiki.js
wikijs-db: Base de datos PostgreSQL para almacenar el contenido de Wiki.js
certbot: Servicio opcional para la gestión de certificados SSL

Requisitos previos

Docker Engine (versión 19.03.0+)
Docker Compose (versión 1.27.0+)
Un servidor con acceso público a internet (para certificados SSL)
Puertos 80 y 443 disponibles

Instalación

Clona este repositorio:

bashgit clone https://github.com/tu-usuario/tu-repo.git
cd tu-repo

Crea las carpetas necesarias para los volúmenes persistentes:

bashmkdir -p data certbot/etc certbot/lib certbot/log nginx

Inicia los servicios:

bashdocker-compose up -d
Acceso a Wiki.js
Una vez que los contenedores estén en funcionamiento, puedes acceder a Wiki.js a través de:

http://localhost:3000 (si accedes desde el servidor)
http://tu-dominio.com (si has configurado un dominio)

En el primer acceso, se te pedirá completar la configuración inicial de Wiki.js.
Configuración de SSL
El archivo docker-compose incluye un servicio Certbot para gestionar certificados SSL. Para configurarlo correctamente:

Detén los servicios:

bashdocker-compose down

Actualiza la configuración de Nginx para incluir tu dominio en lugar de "localhost"
Configura Certbot para obtener certificados para tu dominio:

bashdocker-compose run --rm certbot certonly --webroot --webroot-path=/var/www/certbot -d tu-dominio.com -d www.tu-dominio.com

Actualiza nuevamente la configuración de Nginx para usar los certificados SSL
Reinicia los servicios:

bashdocker-compose up -d
Personalizaciones
Cambio de credenciales de la base de datos
Para mayor seguridad, se recomienda cambiar las credenciales predeterminadas de la base de datos. Modifica las siguientes variables en el archivo docker-compose.yml:
yaml# En el servicio wikijs-db
environment:
  - POSTGRES_USER=tu_usuario
  - POSTGRES_PASSWORD=tu_contraseña_segura
  - POSTGRES_DB=wikijs

## En el servicio wikijs (para que coincidan)
environment:
  - DB_USER=tu_usuario
  - DB_PASS=tu_contraseña_segura
Personalización de Nginx
Si necesitas personalizar la configuración de Nginx, puedes modificar el comando en el servicio de Nginx para que apunte a un archivo de configuración personalizado montado como volumen.
Respaldo de datos
La base de datos PostgreSQL guarda sus datos en el directorio ./data. Para realizar copias de seguridad:

Detén los servicios o asegúrate de que no hay escrituras:

bashdocker-compose pause wikijs wikijs-db

Haz una copia de seguridad del directorio de datos:

bashtar -czf wikijs-backup-$(date +%Y%m%d).tar.gz data/

Reanuda los servicios:

bashdocker-compose unpause wikijs wikijs-db
Solución de problemas
Si encuentras problemas con la implementación, comprueba los logs de los servicios:
bash# Ver logs de todos los servicios
docker-compose logs

## Ver logs de un servicio específico
docker-compose logs wikijs
docker-compose logs nginx
docker-compose logs wikijs-db
