# Documentación Práctica Nginx Con Docker

**Autor:** Jorge Garre Corrales
**Proyecto:** Configurar certificado SSL en un servidor Nginx con contenedor Docker

# Índice

1. [Instalar paquetes necesarios](#1-instalar-paquetes-necesarios)  
   - [Crear usuarios y contraseñas para el acceso web](#12-crear-usuarios-y-contraseñas-para-el-acceso-web)  
   - [Configurar nginx.conf](#13-configurar-nginxconf)  
   - [Probar la nueva configuración](#14-probar-la-nueva-configuración)

2. [Tareas](#2-tareas)  
   - [T.1](#21-t1)  
   - [T.2](#22-t2)  
   - [Combinar autenticación con clave e IP](#23-combinar-autenticación-con-clave-e-ip)

3. [Bloquear acceso a mi máquina anfitriona](#32-bloquear-acceso-a-mi-máquina-anfitriona)

### Prerequisitos

He añadido la siguiente configuración a mi fichero /etc/hosts

![](assets/20251203_201515_image.png)


## 1. Generar certificado

En la imagen se puede ver los comandos que he ejecutado para que me genere el certifcado

![](assets/20251203_203002_image.png)


## 2. Configuración

He añadido la siguiente configuración a mi nginx.conf para que use el certificado SSL

![](assets/20251203_203337_image.png)


Y la siguiente configuración a mi docker-compose.yml para pasarle las claves del certificado SSL


![](assets/20251203_203826_image.png)



Luego he reiniciado el contenedor
