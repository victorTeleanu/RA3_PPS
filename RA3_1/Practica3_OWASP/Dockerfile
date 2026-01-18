FROM victorteleanu/pps:pr2

# Instalamos git temporalmente para bajar las reglas
RUN apt-get update && apt-get install -y git && \
    git clone https://github.com/SpiderLabs/owasp-modsecurity-crs.git /tmp/owasp-modsecurity-crs

# Preparamos el directorio de reglas según tus instrucciones
RUN mkdir -p /etc/modsecurity/rules && \
    cp /tmp/owasp-modsecurity-crs/crs-setup.conf.example /etc/modsecurity/crs-setup.conf && \
    cp /tmp/owasp-modsecurity-crs/rules/*.conf /etc/modsecurity/rules/ && \
    cp /tmp/owasp-modsecurity-crs/rules/*.data /etc/modsecurity/rules/

# Limpiamos para que la imagen sea ligera
RUN rm -rf /tmp/owasp-modsecurity-crs && apt-get purge -y git && apt-get autoremove -y

# Copiamos nuestra configuración que carga las reglas y la regla de test
COPY conf/security2.conf /etc/apache2/mods-available/security2.conf
COPY conf/owasp-testing.conf /etc/apache2/conf-available/owasp-testing.conf

# Habilitamos la configuración de testing
RUN a2enconf owasp-testing

EXPOSE 80 443