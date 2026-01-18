# 🛡️ Práctica 3: OWASP

Esta práctica representa el endurecimiento del servidor. Hemos integrado el **OWASP Core Rule Set (CRS)**, un conjunto de reglas de detección de ataques de código abierto que protege al servidor contra las vulnerabilidades más críticas identificadas por la comunidad (OWASP Top 10).

## 📂 Estructura del directorio

```text
Practica3_OWASP/
├── Dockerfile
└── conf/
    ├── security2.conf
    └── owasp-testing.conf
```

## ⚙️ Configuración realizada
Para llegar al estado final del servidor, se realizaron las siguientes acciones técnicas:

### A. Implementación del Core Rule Set (CRS)
Se ha automatizado el despliegue del repositorio oficial de **SpiderLabs** para garantizar que el servidor cuente con las firmas de ataque más actualizadas:

* **Clonación**: `git clone https://github.com/SpiderLabs/owasp-modsecurity-crs.git`.
* **Organización**: Se han migrado las reglas y los archivos de configuración (`crs-setup.conf`) al directorio persistente `/etc/modsecurity/`.

### B. Sistema de puntuación de anomalías (Anomaly Scoring)
A diferencia de las reglas estáticas, el CRS utiliza un modelo de puntuación: cada elemento sospechoso en una petición suma puntos. Si el total supera el umbral establecido (por defecto 5), ModSecurity bloquea la petición.

### C. Regla de validación personalizada
Se ha configurado una regla de prioridad para verificar el funcionamiento del motor en tiempo real:

* **Directiva**: `SecRule ARGS:testparam "@contains test" "id:1234,deny,status:403,msg:'Cazado por Ciberseguridad'"`

## 🛠️ Dockerfile

```dockerfile
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
```

## 🔍 Validación

Se han ejecutado pruebas de penetración para confirmar que tanto las reglas personalizadas como el conjunto de reglas de **OWASP** bloquean el tráfico malicioso de forma efectiva:

| Tipo de ataque | Comando de prueba | Respuesta | Origen del bloqueo |
| :--- | :--- | :--- | :--- |
| **Parámetro Prohibido** | `?testparam=test` | 403 Forbidden | Regla Local (ID 1234) |
| **Command Injection** | `?exec=/bin/bash` | 403 Forbidden | OWASP (Score: 5) |
| **Path Traversal** | `?exec=/../../` | 403 Forbidden | OWASP (Score: 30) |

![Validación práctica 3](../assets/verificacion_pr3.png)

## 🌐 Docker Hub
Imagen disponible para su despliegue de forma rápida:

| Campo | Valor |
| :--- | :--- |
| **Repositorio** | `victorteleanu/pps` |
| **Etiqueta (Tag)** | `pr3` |
| **Comando Pull** | `docker pull victorteleanu/pps:pr3` |