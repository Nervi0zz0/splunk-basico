# Splunk para Analistas SOC

---

## Visión General

Splunk es una plataforma avanzada para el análisis de grandes volúmenes de datos y la gestión de logs, diseñada especialmente para monitorear, analizar y visualizar datos en tiempo real. Es una herramienta esencial en el campo de la ciberseguridad, particularmente en funciones de SIEM (Gestión de Información y Eventos de Seguridad), donde ayuda a identificar y responder a incidentes de seguridad.

---

## Funcionalidades

### Monitoreo en Tiempo Real
Splunk permite la visualización de eventos y métricas en tiempo real, lo que proporciona una capacidad crítica para la detección inmediata de amenazas y anomalías en la infraestructura de TI. A través de dashboards personalizables, los usuarios pueden observar de manera directa el comportamiento de sistemas y redes.

### Análisis de Datos
Con Splunk, es posible realizar análisis detallados sobre grandes volúmenes de datos, permitiendo descubrir patrones, correlacionar eventos y extraer información valiosa para la toma de decisiones en seguridad. La plataforma es capaz de procesar datos estructurados y no estructurados, lo que la convierte en una solución flexible y escalable.

### Integración con Otras Herramientas
Splunk cuenta con una amplia gama de Apps y Add-ons que permiten integrar fuentes de datos externas y herramientas de seguridad. Esta integración permite centralizar los análisis y obtener una visión unificada de los eventos de seguridad.

---

## Casos de Uso

### Gestión de Logs
La recolección, indexación y análisis de logs es uno de los casos de uso más comunes en Splunk. La plataforma puede ingerir logs de múltiples fuentes (servidores, dispositivos de red, aplicaciones) y proporcionar análisis detallados sobre estos datos. Los logs son esenciales para identificar patrones de comportamiento y eventos anómalos.

### SIEM con Splunk
Splunk actúa como un potente SIEM, centralizando los eventos de seguridad para su análisis y correlación. La plataforma permite a los equipos de seguridad detectar amenazas, responder rápidamente a incidentes y automatizar acciones basadas en alertas y reglas personalizadas.

### Respuesta a Incidentes
Splunk también es una herramienta invaluable en la respuesta a incidentes, permitiendo a los analistas de seguridad investigar en profundidad los eventos registrados, identificar la causa raíz de los incidentes y realizar un seguimiento de las acciones correctivas. Además, la plataforma facilita la generación de informes detallados para su documentación.

---

## Comandos de Splunk

### Comandos Básicos

* Comando:
    ```spl
    index="main"
    ```
    Descripción: Filtra los eventos del índice "main". Utilizado para trabajar con datos específicos de un índice.
* Comando:
    ```spl
    source="logfile.log"
    ```
    Descripción: Filtra los eventos provenientes de un archivo de log específico.
* Comando:
    ```spl
    sourcetype="json"
    ```
    Descripción: Filtra eventos según el tipo de fuente, como "json".
* Comando:
    ```spl
    host="server1"
    ```
    Descripción: Filtra los eventos provenientes de un host específico.
* Comando:
    ```spl
    *error*
    ```
    Descripción: Realiza una búsqueda de eventos que contengan la palabra "error".
* Comando:
    ```spl
    status=200
    ```
    Descripción: Filtra los eventos en los que el campo "status" tenga el valor 200 (respuesta exitosa HTTP).
* Comando:
    ```spl
    | stats count
    ```
    Descripción: Realiza una agregación básica, contando la cantidad de eventos en el conjunto de datos filtrados.

### Comandos de Búsqueda Avanzada

* Comando:
    ```spl
    index="main" sourcetype="json" | stats count by status
    ```
    Descripción: Filtra eventos por el índice "main" y el sourcetype "json", luego realiza un conteo de eventos agrupados por "status".
* Comando:
    ```spl
    index="main" error | top 10 source
    ```
    Descripción: Filtra eventos que contienen la palabra "error" y muestra las 10 principales fuentes de esos eventos.
* Comando:
    ```spl
    index="security" | timechart span=1h count
    ```
    Descripción: Muestra una gráfica con la cantidad de eventos por hora, útil para analizar eventos temporales.
* Comando:
    ```spl
    index="logs" | stats avg(bytes) by host
    ```
    Descripción: Calcula el promedio de "bytes" por cada host, ideal para analizar el volumen de datos por servidor.
* Comando:
    ```spl
    index="main" | dedup source
    ```
    Descripción: Elimina registros duplicados basados en el campo "source".

### Comandos de Seguridad

* Comando:
    ```spl
    index="security" sourcetype="syslog" | stats count by host
    ```
    Descripción: Filtra eventos de tipo "syslog" en el índice "security" y muestra un conteo agrupado por host.
* Comando:
    ```spl
    index="firewall" | stats values(ip) by action
    ```
    Descripción: Muestra las IP asociadas con cada acción del firewall (permitir o bloquear), útil para detectar intentos sospechosos.
* Comando:
    ```spl
    index="security" | search "failed login" | stats count by user
    ```
    Descripción: Busca eventos con "failed login" y agrupa el conteo por usuario.
* Comando:
    ```spl
    index="security" | search "brute force" | stats count
    ```
    Descripción: Busca eventos con "brute force" para identificar intentos de ataque por fuerza bruta.
* Comando:
    ```spl
    index="firewall" | search "blocked" | stats count by ip
    ```
    Descripción: Filtra eventos de firewall que indican tráfico bloqueado y muestra la cantidad de intentos por IP.

### Comandos de Tiempo y Ordenación

* Comando:
    ```spl
    index="main" | earliest=-24h@h latest=now
    ```
    Descripción: Filtra eventos ocurridos en las últimas 24 horas, ideal para obtener eventos recientes.
* Comando:
    ```spl
    index="main" | earliest="2024-12-01T00:00:00" latest="2024-12-05T23:59:59"
    ```
    Descripción: Filtra eventos dentro de un rango de fechas específico, útil para auditorías.
* Comando:
    ```spl
    index="main" | sort - _time
    ```
    Descripción: Ordena los eventos por el campo "_time" en orden descendente, mostrando los eventos más recientes.
* Comando:
    ```spl
    index="main" | sort + bytes
    ```
    Descripción: Ordena los eventos por el campo "bytes" en orden ascendente, útil para detectar picos de tráfico.
* Comando:
    ```spl
    index="main" | head 10
    ```
    Descripción: Muestra los primeros 10 eventos de la búsqueda actual, ideal para un resumen rápido.

### Comandos de Agregación y Análisis

* Comando:
    ```spl
    index="web" | stats sum(bytes) by host
    ```
    Descripción: Suma los valores de "bytes" por cada host, útil para ver el volumen de datos procesados por cada servidor.
* Comando:
    ```spl
    index="web" | timechart span=1h avg(bytes)
    ```
    Descripción: Muestra una gráfica con el promedio de bytes procesados por hora, ideal para detectar tendencias de tráfico.
* Comando:
    ```spl
    index="security" | top 10 src_ip
    ```
    Descripción: Muestra las 10 direcciones IP de origen más frecuentes, útil para detectar posibles puntos de origen de ataques.
* Comando:
    ```spl
    index="main" | stats count by status, host
    ```
    Descripción: Realiza un conteo de eventos por "status" y agrupa por "host", ideal para el análisis de errores por servidor.
* Comando:
    ```spl
    index="main" | chart count over _time by status
    ```
    Descripción: Genera un gráfico de barras con la cantidad de eventos agrupados por "status" a lo largo del tiempo.

---

## Recursos Adicionales

* Documentación Oficial de Splunk: Guías detalladas sobre el uso de Splunk para diversas aplicaciones de seguridad y análisis de datos.
* Splunk Community: Comunidad activa de usuarios que comparten conocimientos y resuelven problemas.
* Splunk Education: Cursos y certificaciones sobre el uso de Splunk.
* TryHackMe: Laboratorios prácticos y desafíos de ciberseguridad.
* Hack The Box: Desafíos interactivos y pruebas de penetración.

---
