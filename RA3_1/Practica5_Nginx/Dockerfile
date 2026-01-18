FROM nginx:latest

USER root

# Instalación de PHP y utilidades
RUN apt-get update && apt-get install -y php-fpm openssl apache2-utils && apt-get clean

# Generación de certificados SSL
RUN mkdir -p /etc/nginx/ssl && \
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout /etc/nginx/ssl/nginx.key \
    -out /etc/nginx/ssl/nginx.crt \
    -subj "/C=ES/ST=Castellon/L=Castellon/O=Ciber/OU=TI/CN=localhost"

# Credenciales
RUN htpasswd -bc /etc/nginx/.htpasswd admin admin123

# PHP escuchando en el puerto 9000 (importante para evitar problemas)
RUN sed -i 's|listen = /run/php/php8.4-fpm.sock|listen = 127.0.0.1:9000|' /etc/php/8.4/fpm/pool.d/www.conf

# Copia de archivos
COPY conf/default.conf /etc/nginx/conf.d/default.conf
COPY www/ /var/www/html/

RUN chown -R www-data:www-data /var/www/html

EXPOSE 80 443
CMD ["bash"]