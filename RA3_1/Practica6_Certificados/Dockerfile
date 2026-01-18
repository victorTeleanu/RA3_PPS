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