# Práctica 7: Apache Hardening & Best Practices

Esta práctica representa la culminación del proceso de endurecimiento (*hardening*), donde se implementa una arquitectura modular de seguridad para blindar el servidor frente a técnicas de reconocimiento y abusos de protocolo.

---

## 1. Estructura del directorio
La configuración se ha modularizado para separar las políticas de seguridad de la lógica del servidor web.

```text
Practica7_ApacheHardening/
├── Dockerfile                # Definición de la imagen y provisión de módulos
├── conf/                     # Configuraciones modulares
│   ├── hardening.conf        # Ocultación de información y timeouts
│   ├── modsecurity.conf      # Configuración del WAF y firma personalizada
│   ├── security-headers.conf # Inyección de cabeceras de protección
│   └── victorteleanu.conf    # VirtualHost principal (SSL y Redirección)
└── www/
│   └── index.html            # Interfaz de estado del sistema protegido
└── README.md
```

---

## 2. Archivos de configuración

### **A. Hardening (`hardening.conf`)**
Se configura la ocultación de la versión de Apache y la mitigación de ataques tipo DoS lento.
* **ServerTokens Full**: Permite que ModSecurity gestione el banner del servidor.
* **ServerSignature Off**: Elimina la firma del servidor de las páginas de error.
* **FileETag None**: Desactiva la generación de ETags para evitar filtración de inodos.
* **TraceEnable Off**: Desactiva el método TRACE para evitar ataques XST.
* **Timeout 60**: Reduce el tiempo de espera para mitigar ataques como Slowloris.

### **B. WAF (`modsecurity.conf`)**
* **SecRuleEngine On**: Activa el motor de reglas de ModSecurity.
* **SecServerSignature**: Cambia la identidad pública del servidor a `"Servidor-Victor-Privado"`.
* **SecAuditLog**: Configura el registro de auditoría en `/var/log/apache2/modsec_audit.log`.

### **C. Cabeceras (`security-headers.conf`)**
Implementación de cabeceras para mitigar ataques en el lado del cliente.
* **X-Frame-Options SAMEORIGIN**: Protege contra ataques de Clickjacking.
* **X-XSS-Protection "1; mode=block"**: Activa el filtro XSS del navegador.
* **X-Content-Type-Options nosniff**: Previene el sniffing de tipos MIME.
* **Set-Cookie HttpOnly;Secure**: Asegura que las cookies solo se transmitan por HTTPS y no sean accesibles vía script.

### **D. VirtualHost (`victorteleanu.conf`)**
* **Redirección**: Fuerza todo el tráfico HTTP (puerto 80) hacia el puerto HTTPS seguro (9449).
* **LimitExcept**: Restricción estricta de métodos HTTP, permitiendo únicamente `GET`, `POST` y `HEAD`.

---

## 3. Dockerfile
El Dockerfile automatiza la instalación de `libapache2-mod-security2`, la generación de certificados RSA de 2048 bits y la aplicación de permisos restrictivos (`chmod 750`) sobre las rutas críticas.

```Dockerfile
FROM php:8.2-apache

# 1. Instalación de ModSecurity y OpenSSL
RUN apt-get update && apt-get install -y \
    libapache2-mod-security2 \
    openssl \
    && apt-get clean

# 2. Generar certificados SSL
RUN mkdir -p /etc/apache2/ssl && \
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout /etc/apache2/ssl/apache.key \
    -out /etc/apache2/ssl/apache.crt \
    -subj "/C=ES/ST=Castellon/L=Castellon/O=Caminas/OU=Hardening/CN=www.victorteleanu.com"

# 3. Copiar configuraciones modulares
COPY conf/hardening.conf /etc/apache2/conf-available/hardening.conf
COPY conf/security-headers.conf /etc/apache2/conf-available/security-headers.conf
COPY conf/modsecurity.conf /etc/modsecurity/modsecurity.conf

# 4. Habilitar módulos, configuraciones y el nuevo VirtualHost
RUN a2enmod ssl headers rewrite unique_id security2 && \
    a2enconf hardening security-headers

COPY conf/victorteleanu.conf /etc/apache2/sites-available/victorteleanu.conf
RUN a2dissite 000-default.conf && a2ensite victorteleanu.conf

# 5. Preparación de logs y permisos
RUN touch /var/log/apache2/modsec_audit.log && \
    chown www-data:www-data /var/log/apache2/modsec_audit.log && \
    chmod -R 750 /etc/apache2 /usr/sbin/apache2

# 6. Despliegue de contenido
COPY www/ /var/www/html/
RUN chown -R www-data:www-data /var/www/html

EXPOSE 80 443
```

---

## 4. Despliegue y uso

### A. Construcción de la imagen
```bash
docker build -t victorteleanu/pps:pr7 .
```

### B. Subir la imagen a DockerHub
```bash
docker push victorteleanu/pps:pr7
```

### C. Despliegue del contenedor
```bash
docker run -d --name practica7_test -p 9007:80 -p 9449:443 victorteleanu/pps:pr7
```

---

## 5. Verificación

A continuación se detallan los comandos utilizados para realizar la verificación.

> **Configuración del Sistema:** Para realizar las verificaciones de forma correcta, es obligatorio añadir la siguiente línea en el archivo `hosts` de su sistema:
> ```bash
> 127.0.0.1 www.victorteleanu.com
> ```

### **A. Verificación de identidad y cabeceras**

Verifica que el servidor oculte su versión real y muestre las cabeceras de seguridad configuradas.
```bash
curl -I -k -s curl -I -k -s https://www.victorteleanu.com:9449
```

**Resultado esperado**: La cabecera `Server` debe mostrar `Servidor-Victor-Privado` y aparecerán las cabeceras de seguridad.

### **B. Verificación de bloqueo de métodos (DELETE)**

Comprueba que el servidor deniegue métodos no permitidos (403 Forbidden).
```bash
curl -I -k -s -X DELETE https://www.victorteleanu.com:9449
```

**Resultado esperado**: Respuesta `HTTP/1.1 403 Forbidden`.

### **C. Verificación de desactivación de TRACE**

Asegura que el método TRACE esté inhabilitado (405 Method Not Allowed).
```bash
curl -I -k -s -X TRACE https://www.victorteleanu.com:9449
```

**Resultado esperado**: Respuesta `HTTP/1.1 405 Method Not Allowed`.

### **D. Verificación de redirección HTTP a HTTPS**

Valida que el acceso por el puerto inseguro 9007 redirija al puerto seguro 9449.
```bash
curl -I -s http://www.victorteleanu.com:9007
```

**Resultado esperado**: Respuesta `HTTP/1.1 301 Moved Permanently` con la cabecera `Location` hacia el puerto 9449.

**Evidencia:**

---

![Verificación práctica 7](../assets/verificacion_pr7.png)

## 6. DockerHub

La imagen final se encuentra en el siguiente enlace:

**[victorteleanu/pps:pr7](https://hub.docker.com/repository/docker/victorteleanu/pps/tags/pr7)**