FROM victorteleanu/pps:pr1

# Instalar el módulo ModSecurity para Apache
RUN apt-get update && apt-get install -y \
    libapache2-mod-security2 \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Configuración base oficial
RUN cp /etc/modsecurity/modsecurity.conf-recommended /etc/modsecurity/modsecurity.conf

# Aplicar nuestra configuración personalizada
COPY conf/modsecurity_custom.conf /etc/apache2/conf-available/modsecurity_custom.conf

# Habilitar el módulo y la nueva configuración
RUN a2enmod security2 && a2enconf modsecurity_custom