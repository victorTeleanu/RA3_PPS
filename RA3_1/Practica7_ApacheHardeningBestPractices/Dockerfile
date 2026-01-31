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