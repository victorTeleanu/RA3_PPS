# 🛡️ RA3_1: Server Hardening

Este repositorio centraliza los trabajos realizados para el **RA3_1 Server Hardening**.

## 🚀 Despliegue con Docker Hub

Para facilitar la auditoría y el despliegue rápido de cada etapa, todas las prácticas han sido empaquetadas y subidas a **Docker Hub**.

### Estrategia de etiquetado
Se ha utilizado una nomenclatura estandarizada para mantener el repositorio organizado y profesional:

* **`pps`**: Identificador del módulo (**Puesta en producción segura**). Actúa como el nombre del repositorio principal.
* **`pr1` a `pr7`**: Etiquetas (*tags*) que representan la evolución incremental de la seguridad. Cada versión hereda las protecciones de la anterior, permitiendo comparar el comportamiento del servidor en diferentes niveles de blindaje.

| Práctica | Descripción Técnica | Docker Tag |
| :--- | :--- | :--- |
| **01** | Endurecimiento inicial, CSP e infraestructura SSL/TLS | `pps:pr1` |
| **02** | Implementación de WAF activo (ModSecurity) | `pps:pr2` |
| **03** | Integración de OWASP Core Rule Set (CRS) | `pps:pr3` |
| **04** | Mitigación de ataques DoS y Brute Force (mod_evasive) | `pps:pr4` |
| **05** | Hardening en Nginx + PHP-FPM 8.4 (Stack TCP/IP) | `pps:pr5` |
| **06** | Gestión de VirtualHosts y Certificados Digitales | `pps:pr6` |
| **07** | Hardening avanzado y Ofuscación (Defense in Depth) | `pps:pr7` |

🔗 **Repositorio Oficial:** [hub.docker.com/r/victorteleanu/pps](https://hub.docker.com/repositories/victorteleanu)

## 📂 Resumen de Prácticas

### 1. CSP & Hardening Base
Fase de reducción de superficie de ataque en Apache. Desactivación de firmas del servidor, eliminación de `autoindex` y configuración de **Content Security Policy** para mitigar ataques XSS.

### 2. Web Application Firewall (WAF)
Introducción de **ModSecurity v2**. Configuración del motor de reglas en modo `On` (bloqueo activo) para interceptar peticiones maliciosas antes de que lleguen al backend.

### 3. OWASP Core Rule Set
Implementación del **CRS de SpiderLabs**. Uso de inteligencia colectiva para bloquear el **OWASP Top 10** (SQLi, XSS, LFI) mediante un sistema de puntuación de anomalías (*Anomaly Scoring*).

### 4. Protección Anti-DDoS
Configuración de **mod_evasive** para actuar como *rate-limiter*. El servidor bloquea automáticamente IPs que superan los umbrales de peticiones por segundo, protegiendo la disponibilidad del servicio.

### 5. Optimización Nginx + PHP 8.4
Migración a Nginx para comparar arquitecturas de seguridad. Resolución de errores **502 Bad Gateway** mediante la implementación de comunicación por **sockets TCP** entre el servidor web y el motor PHP.

### 6. Certificados y Redirecciones
Implementación de protocolos seguros. Generación de certificados **RSA de 2048 bits** y configuración de reglas de reescritura para forzar el tráfico HTTPS en todo el dominio.

### 7. Defensa en Profundidad
Consolidación final. Aplicación de técnicas de **ofuscación** (cambio de firmas de servidor a *"Servidor Privado"*) y endurecimiento de permisos a nivel de sistema de archivos (`chmod 750`) para evitar escalada de privilegios.