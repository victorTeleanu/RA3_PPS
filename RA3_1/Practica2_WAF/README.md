# 🛡️ Práctica 2: Web Application Firewall

En esta segunda fase, mejoramos el servidor endurecido de la Práctica 1 añadiendo una capa de seguridad activa: un **WAF (Web Application Firewall)** basado en **ModSecurity**.

## 📂 Estructura del Directorio

```text
Practica2_WAF/
├── Dockerfile
└── conf/
    └── modsecurity_custom.conf
```

## ⚙️ Configuración realizada
Para llegar al estado final del servidor, se realizaron las siguientes acciones técnicas:

Se ha instalado el motor de ModSecurity y el conjunto de reglas básicas (CRS) sobre la imagen base de la práctica anterior.
* **Comando**: `apt-get install libapache2-mod-security2`.
* **Habilitar módulos necesarios**: `a2enmod security2`.

### B. Activación del motor de bloqueo
Por defecto, ModSecurity se instala en modo de solo detección (*DetectionOnly*). Se ha implementado el archivo `conf/modsecurity_custom.conf` para cambiar el comportamiento a bloqueo activo:

* **Directiva clave**: `SecRuleEngine On`

### C. Herencia de seguridad
Al utilizar la instrucción `FROM victorteleanu/pps:pr1`, el contenedor mantiene automáticamente todas las capas de protección implementadas anteriormente:

* **Cifrado SSL/TLS** (Puerto 443).
* **Cabeceras HSTS y CSP**.
* **Listado de directorios deshabilitado**.

## 🛠️ Dockerfile
El archivo de construcción automatiza la instalación del WAF sobre la base endurecida de la etapa anterior.

```dockerfile
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
```

## 🔍 Validación
Para verificar que el WAF protege el servidor, se simula un ataque de **Directory Traversal** intentando acceder a archivos sensibles del sistema operativo a través de la URL:

```bash
# Intento de lectura de /etc/passwd a través de la URL
curl -k -I "https://localhost/index.html?exec=/etc/passwd"
```

### Resultado de seguridad

El servidor debe denegar el acceso con un código **403 Forbidden** debido a la peligrosidad de la petición detectada.

![Validación práctica 2](../assets/verificacion_pr2.png)

## 🌐 Docker Hub
Imagen disponible para su despliegue de forma rápida:

| Campo | Valor |
| :--- | :--- |
| **Repositorio** | `victorteleanu/pps` |
| **Etiqueta (Tag)** | `pr2` |
| **Comando Pull** | `docker pull victorteleanu/pps:pr2` |