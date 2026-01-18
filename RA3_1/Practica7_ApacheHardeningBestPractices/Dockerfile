FROM php:8.2-apache

# Instalación de ModSecurity y dependencias necesarias
RUN apt-get update && apt-get install -y \
    libapache2-mod-security2 \
    && apt-get clean

# Aplicar configuraciones de Hardening, Cabeceras y WAF
COPY conf/hardening.conf /etc/apache2/conf-available/hardening.conf
COPY conf/security-headers.conf /etc/apache2/conf-available/security-headers.conf
COPY conf/modsecurity.conf /etc/modsecurity/modsecurity.conf

# Habilitar módulos de Apache y las nuevas configuraciones
RUN a2enmod headers rewrite unique_id security2 && \
    a2enconf hardening security-headers

# Preparación del sistema de Auditoría
RUN touch /var/log/apache2/modsec_audit.log && \
    chown www-data:www-data /var/log/apache2/modsec_audit.log

# Hardening de sistema
RUN chmod -R 750 /etc/apache2 /usr/sbin/apache2

# Despliegue del contenido web y permisos
COPY www/ /var/www/html/
RUN chown -R www-data:www-data /var/www/html

EXPOSE 80