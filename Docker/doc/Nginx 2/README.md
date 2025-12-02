# Documentación Práctica Nginx II Autenticación Con Docker

**Autor:** Jorge Garre Corrales
**Proyecto:** Despliegue de servidor web Nginx usando Docker y Docker Compose

## 1. Instalar paquetes necesarios

Lo primero que he hecho ha sido descargar las utilidades de OpenSSL que vienen en un contenedor Docker

![](assets/20251202_142732_image.png)

## 1.2 Crear usuarios y contraseñas para el acceso web

Al crear la clave me ha devuelto un warning por que el docker que se ha ejecutado es para arquitectura amd64 y mi ordenenador es arm64, pero ha creado la clave correctamente

![](assets/20251202_155744_image.png)


## 1.3 Configurar Nginx.conf
