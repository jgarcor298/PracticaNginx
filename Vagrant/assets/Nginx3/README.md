# Práctica 2.3: Acceso seguro con Nginx

## Descripción
Configuración de acceso seguro mediante certificados SSL/TLS autofirmados en Nginx para habilitar HTTPS.

## Requisitos previos
- Práctica 2.1 funcionando correctamente
- Nginx instalado y operativo
- Sitio web configurado (higor.test)

## Entorno
- SO: Debian 11
- Servidor: Nginx 1.18.0
- IP VM: 192.168.56.10
- Dominio: higor.test www.higor.test

## Configuración paso a paso

### 1. Verificar nombre de servidor

Comprobar que el `server_name` está configurado:

```bash
sudo nano /etc/nginx/sites-available/higor.test
```

Debe contener:
```nginx
server_name higor.test www.higor.test;
```

Comprobar sintaxis y recargar:
```bash
sudo nginx -t
sudo systemctl reload nginx
```

### 2. Configurar cortafuegos

Instalar y configurar ufw:
```bash
sudo apt install ufw
sudo ufw allow ssh
sudo ufw allow 'Nginx Full'
sudo ufw delete allow 'Nginx HTTP'
sudo ufw --force enable
sudo ufw status
```

### 3. Generar certificado SSL autofirmado

```bash
sudo openssl req -x509 -nodes -days 365 \
-newkey rsa:2048 -keyout /etc/ssl/private/higor.test.key \
-out /etc/ssl/certs/higor.test.crt
```

Datos a introducir:
```
Country Name: ES
State or Province Name: Andalucía
Locality Name: Motril
Organization Name: IZV
Organizational Unit Name: WEB
Common Name: higor.test
Email Address: webmaster@higor.test
```

### 4. Configurar Nginx con SSL

Editar configuración:
```bash
sudo nano /etc/nginx/sites-available/higor.test
```

Contenido completo:
```nginx
server {
    listen 80;
    listen 443 ssl;
    
    server_name higor.test www.higor.test;
    root /var/www/higor.test/html/static-website-example;
    index index.html index.htm index.nginx-debian.html;
    
    ssl_certificate /etc/ssl/certs/higor.test.crt;
    ssl_certificate_key /etc/ssl/private/higor.test.key;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;
    
    location / {
        try_files $uri $uri/ =404;
    }
}
```

Verificar y recargar:
```bash
sudo nginx -t
sudo systemctl reload nginx
```

### 5. Configurar DNS local (archivo hosts)

**En la máquina anfitriona:**

- **Linux/Mac:** `sudo nano /etc/hosts`
- **Windows:** `C:\Windows\System32\drivers\etc\hosts`

Añadir:
```
192.168.X.X higor.test www.higor.test
```

### 6. Probar

Acceder desde el navegador:
- `http://higor.test`
- `https://higor.test`


## Directivas SSL principales

```nginx
listen 443 ssl;                                      # Puerto HTTPS
ssl_certificate /etc/ssl/certs/higor.test.crt;     # Certificado público
ssl_certificate_key /etc/ssl/private/higor.test.key; # Clave privada
ssl_protocols TLSv1.2 TLSv1.3;                      # Protocolos seguros
ssl_ciphers HIGH:!aNULL:!MD5;                        # Cifrados fuertes
```

## Comandos útiles

```bash
# Verificar sintaxis
sudo nginx -t

# Recargar Nginx
sudo systemctl reload nginx

# Ver puertos en escucha
sudo netstat -tlnp | grep nginx

# Ver logs
sudo tail -f /var/log/nginx/access.log
sudo tail -f /var/log/nginx/error.log

# Ver info del certificado
openssl x509 -in /etc/ssl/certs/higor.test.crt -text -noout

# Probar conexión SSL
openssl s_client -connect higor.test:443
```

## Estructura de archivos

```
/etc/nginx/sites-available/
    └── higor.test                          # Configuración

/etc/ssl/
    ├── certs/higor.test.crt               # Certificado público
    └── private/higor.test.key             # Clave privada

/var/www/higor.test/html/
    └── static-website-example/            # Sitio web
```

## Puertos

| Puerto | Protocolo | Uso |
|--------|-----------|-----|
| 80     | HTTP      | Sin cifrar |
| 443    | HTTPS     | Cifrado SSL/TLS |

## Troubleshooting

### No puedo acceder por HTTPS
```bash
# Verificar puerto 443 abierto
sudo netstat -tlnp | grep :443

# Verificar firewall
sudo ufw status

# Ver errores
sudo tail -f /var/log/nginx/error.log
```

### Error de certificado
```bash
# Verificar archivos existen
ls -l /etc/ssl/certs/higor.test.crt
ls -l /etc/ssl/private/higor.test.key

# Verificar permisos
sudo chmod 644 /etc/ssl/certs/higor.test.crt
sudo chmod 600 /etc/ssl/private/higor.test.key
```

## Comprobaciones para el profesor

```bash
# 1. Mostrar certificados
ls -lh /etc/ssl/certs/higor.test.crt
ls -lh /etc/ssl/private/higor.test.key

# 2. Mostrar configuración
cat /etc/nginx/sites-available/higor.test

# 3. Verificar firewall
sudo ufw status

# 4. Ver puertos en escucha
sudo netstat -tlnp | grep nginx
```

Y mostrar en navegador: `https://higor.test`

## Resumen comandos ejecutados

```bash
# Configurar firewall
sudo apt install ufw
sudo ufw allow ssh
sudo ufw allow 'Nginx Full'
sudo ufw --force enable

# Generar certificado
sudo openssl req -x509 -nodes -days 365 \
-newkey rsa:2048 -keyout /etc/ssl/private/higor.test.key \
-out /etc/ssl/certs/higor.test.crt

# Configurar Nginx
sudo nano /etc/nginx/sites-available/higor.test
sudo nginx -t
sudo systemctl reload nginx
```

---