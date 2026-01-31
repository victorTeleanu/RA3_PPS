# RA3_1: Server Hardening

Este repositorio contiene las actividades para el ejercicio **RA3_1 Server Hardening**. Se presenta una evolución desde un servidor básico hasta una infraestructura blindada bajo el principio de defensa en profundidad.

---

## 1. Estructura del directorio

El proyecto se organiza de forma modular para facilitar la gestión de configuraciones de seguridad:

```text
RA3_1/
├── Assets
├── Practica1_ApacheSSL/
├── Practica2_WAF_Basic/
├── Practica3_WAF_OWASP/
├── Practica4_AntiDoS/
├── Practica5_Nginx_PHP/
├── Practica6_Certificados/
├── Practica7_Hardening/
└── README.md
```

---

##  2. Despliegue con Docker Hub

Para facilitar la auditoría y el despliegue rápido, todas las prácticas están empaquetadas en **Docker Hub**.

### A. Estrategia de etiquetado
* **`pps`**: Identificador del módulo (**Puesta en producción segura**).
* **`pr1` a `pr7`**: Etiquetas (*tags*) que representan la evolución incremental de la seguridad.

### B. Gestión de puertos y contenedores

| Práctica | Puertos (Host:Contenedor) | Etiqueta |
| :--- | :--- | :--- |
| **P1** | 9001:80 - 9443:443 | `pps:pr1` |
| **P2** | 9002:80 - 9444:443 | `pps:pr2` |
| **P3** | 9003:80 - 9445:443 | `pps:pr3` |
| **P4** | 9004:80 - 9446:443 | `pps:pr4` |
| **P5** | 9005:443 | `pps:pr5` |
| **P6** | 9006:80 - 9448:443 | `pps:pr6` |
| **P7** | 9007:80 - 9449:443 | `pps:pr7` |

---

## 3. Resumen de las prácticas

* **1. CSP & Hardening Base**: Reducción de superficie de ataque en Apache mediante desactivación de firmas y políticas de seguridad de contenido (CSP).
* **2. Web Application Firewall**: Despliegue de **ModSecurity v2** en modo preventivo para interceptar peticiones maliciosas en tiempo real.
* **3. OWASP Core Rule Set**: Implementación del conjunto de reglas de **SpiderLabs** para mitigar vulnerabilidades críticas del OWASP Top 10.
* **4. Protección Anti-DDoS**: Configuración de **mod_evasive** para el bloqueo automático de IPs mediante umbrales de peticiones por segundo.
* **5. Nginx**: Migración a arquitectura Nginx resolviendo errores **502 Bad Gateway** mediante el uso de **sockets TCP** para la comunicación con PHP-FPM.
* **6. Certificados**: Generación de certificados **RSA de 2048 bits** personalizados y configuración de redirección forzosa por dominio (`www.victorteleanu.com`).
* **7. Hardening & best practices**: Blindaje final mediante una **estructura modular de archivos .conf**, ocultación total del banner (`Server: Servidor-Victor-Privado`) y restricción estricta de métodos HTTP.