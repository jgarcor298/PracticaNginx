# Servidor Web Nginx con Vagrant y Docker
**Autores:** Jorge Garre Corrales y Higor De Souza Nogueria

**Descripción:** En esta práctica hemos lanzado un servidor web Ngnix desde una máquina virtual de Vagrant y un contenedor de Docker, en la carpeta correspondiente a cada servidor hay un README.md con la documentación de la práctica.

**Servidor Web con Vagrant:** Higor De Souza Nogueira

**Servidor Web con Docker:** Jorge Garre Corrales

# Práctica 2.1: Nginx con Vagrant

## Descripción
Instalación y configuración de servidor web Nginx en Debian usando Vagrant. Se configura un sitio web estático accesible mediante el dominio `higor.test`.

## Entorno
- SO: Debian 11
- Servidor: Nginx 1.18.0
- IP: 192.168.56.10
- Dominio: higor.test

## Instalación rápida

### 1. Levantar VM
```bash
vagrant up
vagrant ssh
```

### 2. Instalar Nginx
```bash
sudo apt update
sudo apt install nginx git -y
```

### 3. Configurar sitio web
```bash
# Crear directorio
sudo mkdir -p /var/www/higor.test/html
cd /var/www/higor.test/html

# Clonar sitio de ejemplo
sudo git clone https://github.com/cloudacademy/static-website-example

# Permisos
sudo chown -R www-data:www-data /var/www/higor.test/html
sudo chmod -R 755 /var/www/higor.test
```

### 4. Configurar Nginx
Crear `/etc/nginx/sites-available/higor.test`:
```nginx
server {
    listen 80;
    listen [::]:80;
    root /var/www/higor.test/html/static-website-example;
    index index.html index.htm index.nginx-debian.html;
    server_name higor.test;
    
    location / {
        try_files $uri $uri/ =404;
    }
}
```

Habilitar sitio:
```bash
sudo ln -s /etc/nginx/sites-available/higor.test /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

### 5. Configurar hosts (máquina local)
Windows: `C:\Windows\System32\drivers\etc\hosts`  
Linux/Mac: `/etc/hosts`

Añadir:
```
192.168.56.10 higor.test
```

## Verificación
Acceder a: `http://higor.test`

Ver logs:
```bash
sudo tail -f /var/log/nginx/access.log
```

## Comandos útiles
```bash
vagrant up/halt/destroy        # Gestión VM
sudo systemctl restart nginx   # Reiniciar servidor
sudo nginx -t                  # Verificar configuración
```

## Estructura
```
/var/www/higor.test/html/static-website-example/
    ├── index.html
    ├── css/
    ├── js/
    └── images/
```
