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