FROM victorteleanu/pps:pr3

# Instalación del módulo mod_evasive
RUN apt-get update && apt-get install -y libapache2-mod-evasive && apt-get clean

# Creación del directorio de logs y asignación de dueño
RUN mkdir -p /var/log/mod_evasive && \
    chown -R root:www-data /var/log/mod_evasive

# Aplicar nuestra configuración personalizada
COPY conf/evasive.conf /etc/apache2/mods-available/evasive.conf

# Habilitar el módulo evasive
RUN a2enmod evasive

EXPOSE 80 443