# DVWA Writeups - Guía de Explotación (Nivel Medium)

## Introducción

DVWA es una aplicación web vulnerable por diseño, ideal para que los profesionales de la seguridad prueben sus habilidades en un entorno legal y controlado. 

Este repositorio documenta el proceso detallado para explotar y evadir las protecciones de las vulnerabilidades más críticas de la web, enfocándose principalmente en el nivel de seguridad **Medium**. A diferencia del nivel básico, aquí analizamos los filtros de seguridad implementados por los desarrolladores y utilizamos técnicas de *bypass* mediante manipulación de cabeceras HTTP, herramientas de desarrollador y *scripting*.

---

## Índice de vulnerabilidades (Writeups)

1. [Brute Force](../RA3_2/Brute_Force/README.md) - Evasión de pausas intencionadas en el servidor (`sleep()`) para extraer credenciales válidas a base de ensayo y error.
2. [Command Injection](../RA3_2/Command_Injection/README.md) - Ejecución de comandos de sistema operativo evadiendo listas negras mediante operadores de consola.
3. [Content Security Policy (CSP) Bypass](../RA3_2/CSP_Bypass/README.md) - Evasión de políticas de seguridad aprovechando un *nonce* estático en las cabeceras HTTP.
4. [Cross Site Request Forgery (CSRF)](../RA3_2/CSRF/README.md) - Falsificación de peticiones saltándose la validación del origen (*Referer*) combinándolo con subida de archivos.
5. [DOM Based Cross Site Scripting (XSS)](../RA3_2/DOM_Based_XSS/README.md) - Inyección de código rompiendo la estructura HTML nativa para ejecutar eventos JavaScript.
6. [File Inclusion](../RA3_2/File_Inclusion/README.md) - Lectura de archivos locales (LFI) críticos del sistema como `/etc/passwd` evadiendo filtros de ruta.
7. [File Upload](../RA3_2/File_Upload/README.md) - Subida de una *Reverse Shell* en PHP burlando la validación del tipo de contenido (`Content-Type`).
8. [JavaScript Attacks](../RA3_2/JavaScript_Attacks/README.md) - Ingeniería inversa a la lógica del frontend para falsificar *tokens* criptográficos desde el navegador.
9. [Reflected Cross Site Scripting (XSS)](../RA3_2/Reflected_XSS/README.md) - Evasión de filtros de etiquetas `<script>` utilizando eventos de error en imágenes (`onerror`).
10. [SQL Injection](../RA3_2/SQL_Injection/README.md) - Extracción de credenciales de la base de datos modificando parámetros POST al vuelo.
11. [SQL Injection (Blind)](../RA3_2/SQL_Injection_Blind/README.md) - Automatización de extracción de datos a ciegas (Boolean-based) letra por letra mediante *scripting* en Python.
12. [Stored Cross Site Scripting (XSS)](../RA3_2/Stored_XSS/README.md) - Inyección persistente bypasseando restricciones en el cliente (`maxlength`) y filtros básicos en el servidor.
13. [Weak Session IDs](../RA3_2/Weak_Session_IDs/README.md) - Secuestro de sesión prediciendo identificadores de *cookies* basados en marcas de tiempo temporales (*timestamps*).

---

## ⚠️ Disclaimer
Toda la información, técnicas y *scripts* de este repositorio tienen un propósito única y exclusivamente educativo. Las pruebas se han realizado en un entorno local y aislado. 

No utilices este conocimiento para atacar sistemas sin el consentimiento explícito y por escrito de sus propietarios. El autor no se hace responsable del mal uso de la información aquí proporcionada. **Hackea con responsabilidad.**