# Práctica 4: Protección Anti-DDoS con mod_evasive

Esta práctica se centra en la disponibilidad del servicio mediante la implementación de **mod_evasive**. El objetivo es detectar y bloquear automáticamente peticiones masivas (ataques DoS/brute force) que superen los umbrales de tráfico definidos.

## 1. Estructura del directorio
El proyecto integra el módulo evasivo sobre la base de seguridad de OWASP de la práctica anterior.

```text
Practica4_DDOS/
├── conf/                   # Configuraciones de seguridad
│   └── evasive.conf        # Definición de umbrales y tiempos de bloqueo
├── Dockerfile              # Construcción basada en pps:pr3
└── README.md
```
## 2. Archivos de configuración

### **A. Configuración de mod_evasive (`evasive.conf`)**
Define el comportamiento del radar de tráfico para identificar abusos:
* **DOSPageCount 5 / DOSPageInterval 1**: Bloquea a un cliente si pide la misma página más de 5 veces en 1 segundo.
* **DOSSiteCount 100 / DOSSiteInterval 2**: Bloquea si se piden más de 100 objetos del sitio en 2 segundos.
* **DOSBlockingPeriod 10**: El atacante es bloqueado por un periodo de 10 segundos cada vez que viola una regla.
* **DOSLogDir**: Directorio donde se almacenan los registros de IPs bloqueadas.

### **B. Integración de logs**
Se ha configurado `/var/log/mod_evasive` con los permisos adecuados para que el proceso de Apache pueda registrar las incidencias de bloqueo en tiempo real.

## 3. Dockerfile
La imagen se construye sobre `pps:pr3`, instalando el binario del módulo y configurando la persistencia de logs antes de habilitar la configuración.

```Dockerfile
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
```

## 4. Despliegue y uso

### A. Construcción de la imagen
```bash
docker build -t victorteleanu/pps:pr4 .
```

### B. Subir la imagen a DockerHub
```bash
docker push victorteleanu/pps:pr4
```

### C. Despliegue del contenedor
```bash
docker run -d --name practica4_test -p 9004:80 -p 9446:443 victorteleanu/pps:pr4
```

## 5. Verificación

Para validar la efectividad de la protección, se realiza una prueba de estrés mediante la herramienta **ApacheBench (ab)** desde dentro del contenedor para simular una carga masiva.

### **Prueba de carga (Stress Test)**

Accedemos al contenedor y ejecutamos una ráfaga de 100 peticiones concurrentes:

- Acceso al contenedor:
```bash
docker exec -it practica4_test bash
# En caso de estar en windows
winpty docker exec -it practica4_test bash
```

- Ejecución de ApacheBench

```bash
ab -n 100 -c 5 http://localhost/index.html
```

**Resultado esperado**: Se observará un alto número de Failed requests (código 403 Forbidden enviado por mod_evasive).

**Evidencia:**

![Verificación práctica 4](../assets/verificacion_pr4.png)

## 6. DockerHub

La imagen final se encuentra en el siguiente enlace:

**[victorteleanu/pps:pr4](https://hub.docker.com/repository/docker/victorteleanu/pps/tags/pr4)**
