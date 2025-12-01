# Práctica 2.2: Autenticación en Nginx

## Descripción
Configuración de autenticación básica HTTP y restricción de acceso por IP en Nginx. Se implementan diferentes niveles de seguridad combinando usuarios/contraseñas con control de acceso por dirección IP.

## Requisitos previos
- Práctica 2.1 funcionando correctamente
- Nginx instalado y operativo
- Sitio web configurado (higor.test)
- Paquete openssl instalado

## Entorno
- SO: Debian 11
- Servidor: Nginx 1.18.0
- IP VM: 192.168.56.10
- Dominio: higor.test
- Herramienta: openssl (para cifrado de contraseñas)

## Configuración paso a paso

### 1. Verificar openssl
```bash
dpkg -l | grep openssl
# Si no está instalado:
sudo apt install openssl -y
```

### 2. Crear usuarios y contraseñas

Crear archivo de contraseñas:
```bash
# Usuario 1
sudo sh -c "echo -n 'higor:' >> /etc/nginx/.htpasswd"
sudo sh -c "openssl passwd -apr1 >> /etc/nginx/.htpasswd"

# Usuario 2
sudo sh -c "echo -n 'apellido:' >> /etc/nginx/.htpasswd"
sudo sh -c "openssl passwd -apr1 >> /etc/nginx/.htpasswd"
```

Verificar usuarios creados:
```bash
cat /etc/nginx/.htpasswd
```

### 3. Configurar autenticación básica (sitio completo)

Editar `/etc/nginx/sites-available/higor.test`:
```nginx
server {
    listen 80;
    listen [::]:80;
    root /var/www/higor.test/html/static-website-example;
    index index.html index.htm index.nginx-debian.html;
    server_name higor.test;
    
    location / {
        auth_basic "Área restringida";
        auth_basic_user_file /etc/nginx/.htpasswd;
        try_files $uri $uri/ =404;
    }
}
```

Reiniciar Nginx:
```bash
sudo nginx -t
sudo systemctl restart nginx
```

### 4. Autenticación solo en contact.html

Modificar configuración para proteger solo una sección:
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
    
    location /contact.html {
        auth_basic "Área restringida - Contacto";
        auth_basic_user_file /etc/nginx/.htpasswd;
    }
}
```

### 5. Restricción por IP (denegar acceso)

Bloquear acceso a la máquina anfitriona:
```nginx
server {
    listen 80;
    listen [::]:80;
    root /var/www/higor.test/html/static-website-example;
    index index.html index.htm index.nginx-debian.html;
    server_name higor.test;
    
    location / {
        deny 192.168.56.1;    # IP de la máquina anfitriona
        allow all;
        try_files $uri $uri/ =404;
    }
}
```

### 6. Combinación IP + Autenticación (satisfy all)

Configuración final - requiere AMBAS condiciones:
```nginx
server {
    listen 80;
    listen [::]:80;
    root /var/www/higor.test/html/static-website-example;
    index index.html index.htm index.nginx-debian.html;
    server_name higor.test;
    
    location / {
        satisfy all;    # Requiere IP válida Y usuario válido
        
        # Control de acceso por IP
        allow 192.168.56.1;    # IP de la máquina anfitriona
        deny all;
        
        # Autenticación básica
        auth_basic "Área restringida";
        auth_basic_user_file /etc/nginx/.htpasswd;
        
        try_files $uri $uri/ =404;
    }
}
```

## Directiva satisfy

### satisfy all
Requiere que se cumplan **AMBAS** condiciones:
- IP válida
- Usuario y contraseña correctos

### satisfy any
Requiere que se cumpla **AL MENOS UNA** condición:
- IP válida **O** usuario válido

```nginx
location / {
    satisfy any;    # Cambia el comportamiento
    allow 192.168.56.1;
    deny all;
    auth_basic "Área restringida";
    auth_basic_user_file /etc/nginx/.htpasswd;
}
```

## Verificación y pruebas

### Ver logs de autenticación
```bash
# Intentos de acceso
sudo tail -f /var/log/nginx/access.log

# Errores de autenticación
sudo tail -f /var/log/nginx/error.log
```

### Códigos de respuesta HTTP
- `200 OK` - Acceso exitoso
- `401 Unauthorized` - Credenciales inválidas o no proporcionadas
- `403 Forbidden` - IP bloqueada o sin permisos

### Probar diferentes escenarios

1. **Usuario inválido**: Introduce usuario/contraseña incorrectos
2. **Cancelar autenticación**: Click en "Cancelar" en el diálogo
3. **IP bloqueada**: Accede desde IP no permitida
4. **Acceso válido**: IP correcta + credenciales correctas

## Comandos útiles

```bash
# Verificar sintaxis de configuración
sudo nginx -t

# Reiniciar Nginx
sudo systemctl restart nginx

# Recargar configuración (sin downtime)
sudo systemctl reload nginx

# Ver estado del servicio
sudo systemctl status nginx

# Ver últimas 20 líneas de logs
sudo tail -20 /var/log/nginx/access.log
sudo tail -20 /var/log/nginx/error.log

# Seguir logs en tiempo real
sudo tail -f /var/log/nginx/access.log
```

## Estructura de archivos

```
/etc/nginx/
    ├── .htpasswd                    # Usuarios y contraseñas cifradas
    ├── sites-available/
    │   └── higor.test              # Configuración del sitio
    └── sites-enabled/
        └── higor.test              # Symlink al sitio activo

/var/log/nginx/
    ├── access.log                   # Registro de accesos
    └── error.log                    # Registro de errores
```

## Orden de evaluación de directivas

Las directivas `allow` y `deny` se evalúan **en orden de aparición**:

```nginx
location / {
    deny 192.168.1.2;      # 1º: Denegar esta IP específica
    allow 192.168.1.0/24;  # 2º: Permitir esta red
    allow 127.0.0.1;       # 3º: Permitir localhost
    deny all;              # 4º: Denegar todo lo demás
}
```

⚠️ **Importante**: `deny all;` siempre debe ir al final

## Notas de seguridad

- La autenticación básica HTTP **NO es segura** sin HTTPS
- Las credenciales se envían en Base64 (fácilmente decodificable)
- Para producción, usar HTTPS + autenticación básica
- Considerar alternativas más seguras (OAuth, JWT, etc.)

## Troubleshooting

### Error: 401 Unauthorized
- Verificar que el archivo `.htpasswd` existe y tiene permisos correctos
- Comprobar que la ruta en `auth_basic_user_file` es correcta
- Verificar usuario y contraseña

### Error: 403 Forbidden
- Comprobar configuración de `allow`/`deny`
- Verificar que la IP está permitida
- Revisar `error.log` para más detalles

### El navegador no pide autenticación
- Limpiar caché del navegador
- Usar ventana de incógnito/privada
- Verificar que `auth_basic` está configurado correctamente

### Cambios no se aplican
```bash
# Siempre verificar sintaxis primero
sudo nginx -t

# Reiniciar el servicio
sudo systemctl restart nginx
```
