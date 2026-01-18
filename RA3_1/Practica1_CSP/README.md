# 🛡️ Práctica 1: CSP

Esta práctica consiste en la implementación de medidas iniciales de **endurecimiento (hardening)** sobre un servidor web Apache corriendo en un contenedor Docker. El objetivo principal es reducir la superficie de ataque y garantizar la integridad y privacidad de las comunicaciones mediante el uso de cabeceras de seguridad y cifrado SSL/TLS.

---

## 📂 Estructura del directorio

```text
Practica1_CSP/
├── Dockerfile
├── conf/
│   ├── default-ssl.conf
│   └── user-hardening.conf
└── ssl/
    ├── apache.crt
    └── apache.key
```

## ⚙️ Configuración realizada
Para llegar al estado final del servidor, se realizaron las siguientes acciones técnicas:

Se desactivó el listado automático de archivos para evitar que atacantes vean la estructura del servidor.
* **Deshabilitar autoindex**: `a2dismod -f autoindex`.
* **Habilitar módulos necesarios**: `a2enmod ssl headers rewrite`.

Se generó un certificado autofirmado utilizando OpenSSL para habilitar el tráfico cifrado:

```bash
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
-keyout ssl/apache.key \
-out ssl/apache.crt \
-subj "/C=ES/ST=Valencia/L=Valencia/O=GVA/OU=PPS/CN=localhost"
```

En el archivo `conf/user-hardening.conf` se definieron las políticas de seguridad que se inyectan en cada respuesta HTTP para proteger al cliente:
* **HSTS**: `Header always set Strict-Transport-Security "max-age=63072000; includeSubDomains"`.
* **CSP**: `Header always set Content-Security-Policy "default-src 'self';"`.

Se modificó el archivo `conf/default-ssl.conf` para establecer la identidad del servidor y activar el cifrado de datos:
* **SSLEngine**: `on`.
* **SSLCertificateFile**: `/etc/apache2/ssl/apache.crt`.
* **SSLCertificateKeyFile**: `/etc/apache2/ssl/apache.key`.

## 🛠️ Dockerfile

```dockerfile
FROM debian:bookworm-slim

# Instalación de paquetes básicos
RUN apt-get update && apt-get install -y \
    apache2 \
    openssl \
    curl \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Habilitar módulos de seguridad y cifrado
RUN a2enmod ssl headers rewrite

# Deshabilitar el listado de directorios
RUN a2dismod -f autoindex

# Crear directorio para certificados
RUN mkdir -p /etc/apache2/ssl

# Copiamos la infraestructura de seguridad
COPY ssl/apache.key /etc/apache2/ssl/
COPY ssl/apache.crt /etc/apache2/ssl/

# Copiamos las reglas de Hardening (CSP, HSTS)
COPY conf/user-hardening.conf /etc/apache2/conf-available/user-hardening.conf

# Copiamos el VirtualHost SSL
COPY conf/default-ssl.conf /etc/apache2/sites-available/default-ssl.conf

RUN a2enconf user-hardening && a2ensite default-ssl

EXPOSE 80 443

CMD ["apache2ctl", "-D", "FOREGROUND"]
```

## 🔍 Validación
La verificación se realiza inspeccionando las cabeceras de seguridad desde el interior del contenedor para asegurar que las políticas se aplican correctamente.

```bash
curl -k -I https://localhost
```

### Resultados obtenidos

![Validación práctica 1](../assets/verificacion_pr1.png)

* **HSTS**: `Strict-Transport-Security: max-age=63072000; includeSubDomains`
* **CSP**: `Content-Security-Policy: default-src 'self';`

## 🌐 Docker Hub
Imagen disponible para su despliegue de forma rápida:

| Campo | Valor |
| :--- | :--- |
| **Repositorio** | `victorteleanu/pps` |
| **Etiqueta (Tag)** | `pr1` |
| **Comando Pull** | `docker pull victorteleanu/pps:pr1` |
