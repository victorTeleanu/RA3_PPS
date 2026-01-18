# 🛡️ Práctica 6: Certificados

En esta fase hemos implementado el cifrado de extremo a extremo mediante el protocolo **SSL/TLS**. El objetivo es garantizar la integridad y confidencialidad de los datos mediante el uso de certificados digitales y la redirección forzosa de todo el tráfico no seguro (HTTP) hacia el canal cifrado (HTTPS).

## 📂 Estructura del directorio

```text
Practica6_Apache/
├── Dockerfile
├── conf/
│   └── midominio.conf
└── www/
    └── index.html
```
## ⚙️ Configuración realizada

### A. Generación de certificado auto-firmado
Siguiendo los estándares de criptografía, se ha generado una clave privada y un certificado X.509 utilizando OpenSSL:
* **Algoritmo**: RSA de 2048 bits.
* **Validez**: 365 días.
* **Common Name (CN)**: `www.midominioseguro.com`.
* **Ruta**: `/etc/apache2/ssl/`.

### B. Redirección forzosa y VirtualHosts
Se ha modificado la lógica del servidor para que no permita tráfico en texto plano:
* **Puerto 80**: Implementa un `Redirect permanent /` hacia la URL segura.
* **Puerto 443**: Activa el `SSLEngine` vinculando los archivos `apache.crt` y `apache.key`.

## 🛠️ Dockerfile

```dockerfile
FROM php:8.2-apache

# Activar el módulo SSL
RUN a2enmod ssl

# Crear subdirectorio y generar certificado auto-firmado
RUN mkdir -p /etc/apache2/ssl && \
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout /etc/apache2/ssl/apache.key \
    -out /etc/apache2/ssl/apache.crt \
    -subj "/C=ES/ST=Castellon/L=Castellon/O=Caminas/OU=ServerDev/CN=www.midominioseguro.com"

# Configurar y activar el host virtual
COPY conf/midominio.conf /etc/apache2/sites-available/midominio.conf
RUN a2dissite 000-default.conf && a2ensite midominio.conf

# Copiar contenido web
COPY www/ /var/www/html/

EXPOSE 80 443
```
## 🔍 Validación

Para verificar la correcta implementación, es indispensable configurar el archivo **hosts** del sistema anfitrión para resolver el dominio simulado:

* **Ruta**: `C:\Windows\System32\drivers\etc\hosts`
* **Añadir al final**: `127.0.0.1 www.midominioseguro.com`

### Resultados de la prueba de conexión

Accedemos a 'http://www.midominioseguro.com' para realizar la validación.

![Validación práctica 6](../assets/verificacion_pr6.png)

## 🌐 Docker Hub

Imagen disponible para su despliegue de forma rápida:

| Campo | Valor |
| :--- | :--- |
| **Repositorio** | `victorteleanu/pps` |
| **Etiqueta (Tag)** | `pr6` |
| **Comando Pull** | `docker pull victorteleanu/pps:pr6` |