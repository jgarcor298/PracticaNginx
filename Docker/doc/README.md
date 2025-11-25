# Documentación Práctica Nginx Con Docker

**Autor:** Jorge Garre Corrales
**Proyecto:** Despliegue de servidor web Nginx usando Docker y Docker Compose


## Índice

1. [Instalación de Docker](#1-instalación-de-docker)
2. [Creación de la estructura de carpetas](#2-creación-de-la-estructura-de-carpetas)
3. [Crear y ejecutar el contenedor Docker](#3-crear-y-ejecutar-el-contenedor-docker)
   3.1 [Editar archivo /etc/hosts](#31-editar-archivo-etchosts)
   3.2 [Comprobar registros del servidor](#32-comprobar-registros-del-servidor)
   3.3 [Detener y eliminar contenedor](#33-detener-y-eliminar-contenedor)


## 1. Instalación de Docker

Lo primero que he hecho ha sido comprobar que tengo instalado Docker.

![](assets/20251125_161913_image.png)


## 2. Creación de la estructura de carpetas

En la imagen se ve a la izquierda la estructura de carpetas, y como en la carpeta html esta clonado el repositorio de la práctica, además se ve el contenido de el fichero de configuración nginx.conf

![](assets/20251125_160733_image.png)


## 3. Crear y ejecutar el contenedor Docker

Para el contenedor Docker he decidido hacerlo con Compose, por lo cual he tenido que crear un fichero docker-compose.yml, con la siguiente configuración.

![](assets/20251125_160804_image.png)


Ahora lanzamos el contenedor para comprobar que la configuración es correcta.

![](assets/20251125_154327_image.png)


Y en el navegador comprobamos que el servidor funciona

![](assets/20251125_160035_image.png)


### 3.1 Editar archivo /etc/hosts

![](assets/20251125_161130_image.png)


Una vez añadido el nombre del dominio y al haber movido la carpeta html con el repositorio, me aparece el index.html del repositorio que hemos descargado.


![](assets/20251125_161348_image.png)


### 3.2 Comprobar registros del servidor

![](assets/20251125_162104_image.png)


### 3.3 Detener y eliminar contenedor

![](assets/20251125_161647_image.png)
