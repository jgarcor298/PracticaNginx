# Documentación Práctica Nginx Con Docker

**Autor:** Jorge Garre Corrales
**Proyecto:** Configurar certificado SSL en un servidor Nginx con contenedor Docker


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
