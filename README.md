# Splunk para Analistas SOC (Guía Técnica)

Guía Práctica y Referencia Avanzada de Splunk para Operaciones del Centro de Seguridad (SOC)

---

## ⚙️ Capacidades Clave de Splunk para el SOC

Splunk es la plataforma central para muchas operaciones de SOC, permitiendo:

* **Monitorización y Alertas en Tiempo Real:** Detectar amenazas y anomalías al instante mediante dashboards y alertas correlacionadas.
* **Investigación Profunda y Threat Hunting:** Analizar terabytes de datos para seguir rastros, buscar IOCs y descubrir ataques complejos.
* **Soporte a la Respuesta a Incidentes (IR):** Facilitar el análisis forense, la determinación del alcance y la causa raíz de los incidentes.
* **Inteligencia y Visualización de Datos:** Generar insights sobre la postura de seguridad, tendencias y cumplimiento a través de informes y visualizaciones.
* **Integración del Ecosistema de Seguridad:** Unificar la visibilidad y coordinar acciones conectando con EDR, Firewalls, Intel de Amenazas, SOAR, etc.

---

## 🎯 Casos de Uso Críticos en el SOC

Splunk es vital para tareas como:

1.  **Detección de Amenazas (SIEM):** Correlacionar eventos, aplicar reglas de detección (ES, personalizadas), identificar anomalías (UBA).
2.  **Gestión Centralizada de Logs:** Ingesta, normalización (CIM), indexación y búsqueda eficiente para análisis y cumplimiento.
3.  **Investigación de Incidentes:** Analizar logs relevantes, reconstruir cronologías, identificar entidades implicadas.
4.  **Threat Hunting Proactivo:** Búsqueda basada en hipótesis (MITRE ATT&CK), búsqueda de IOCs, identificación de actividades inusuales.
5.  **Monitorización de Seguridad Continua:** Vigilancia de accesos, cambios críticos, tráfico de red sospechoso.
6.  **Cumplimiento y Auditoría:** Generación de informes normativos y auditoría de actividades.

---

## 🔥 Comandos de Splunk (SPL)

Dominar **SPL (Search Processing Language)** es fundamental. A continuación, una referencia de comandos útiles organizados por categoría.

### Comandos Básicos

* **Filtrar por Índice (`index`):** Selecciona el contenedor de datos donde buscar. Fundamental para empezar.
    ```spl
    index="wineventlog"
    ```
* **Filtrar por Fuente (`source`):** Especifica el archivo o ruta de origen de los datos.
    ```spl
    source="/var/log/secure"
    ```
* **Filtrar por Tipo de Fuente (`sourcetype`):** Filtra por el tipo de dato definido durante la ingesta (afecta cómo se parsean los campos).
    ```spl
    sourcetype="cisco:asa"
    ```
* **Filtrar por Host (`host`):** Selecciona eventos de una máquina específica.
    ```spl
    host="srv-dc01"
    ```
* **Búsqueda por Palabra Clave:** Busca términos específicos en los eventos (AND implícito entre términos). Usa comillas para frases.
    ```spl
    "failed login" user="admin"
    ```
* **Filtrar por Valor de Campo:** Busca un valor exacto en un campo extraído (sensible a mayúsculas).
    ```spl
    status=404 action="blocked"
    ```
* **Comando `stats` (Básico):** Realiza agregaciones estadísticas. El más simple es contar eventos.
    ```spl
    index=firewall | stats count
    ```

### Comandos de Búsqueda Avanzada (Transformación)

* **`stats` (Agregación):** Calcula estadísticas agrupadas por campos.
    * Descripción: Cuenta eventos del índice "main" (sourcetype json) agrupados por el campo "status".
    ```spl
    index="main" sourcetype="json" | stats count by status
    ```
* **`top` (Valores Más Frecuentes):** Muestra los N valores más comunes de un campo.
    * Descripción: Busca eventos con "error" en el índice "main" y muestra las 10 `source` más frecuentes.
    ```spl
    index="main" error | top limit=10 source
    ```
* **`timechart` (Gráficos Temporales):** Genera gráficos de series temporales.
    * Descripción: Cuenta eventos por hora en el índice "security". Ideal para ver tendencias.
    ```spl
    index="security" | timechart span=1h count
    ```
* **`stats` (Funciones Estadísticas):** Calcula promedios, sumas, máximos, mínimos, etc.
    * Descripción: Calcula el tamaño promedio en bytes (`avg(bytes)`) para cada `host` en el índice "logs".
    ```spl
    index="logs" | stats avg(bytes) by host
    ```
* **`dedup` (Eliminar Duplicados):** Elimina eventos duplicados basados en los valores de ciertos campos.
    * Descripción: Mantiene solo un evento por cada valor único del campo `source`.
    ```spl
    index="main" | dedup source
    ```

### Comandos de Seguridad Específicos

* **Contar por Host (Syslog):** Analiza la volumetría de logs por host.
    ```spl
    index="security" sourcetype="syslog" | stats count by host
    ```
* **Valores Únicos por Acción (Firewall):** Muestra las IPs únicas asociadas a acciones como 'allow' o 'deny'.
    ```spl
    index="firewall" | stats values(src_ip) as SourceIPs by action
    ```
* **Conteo de Logins Fallidos por Usuario:** Identifica usuarios con múltiples intentos fallidos.
    ```spl
    index="security" sourcetype="WinEventLog:Security" EventCode=4625 Account_Name!="*$" | stats count by Account_Name | sort - count
    ```
* **Búsqueda de Fuerza Bruta:** Busca términos asociados a ataques de fuerza bruta.
    ```spl
    index="security" ("brute force" OR "password spraying") | stats count
    ```
* **Contar Bloqueos de Firewall por IP:** Identifica IPs origen que generan más tráfico bloqueado.
    ```spl
    index="firewall" action=blocked | stats count by src_ip | sort - count
    ```

### Comandos de Tiempo y Ordenación

* **Selección Relativa de Tiempo (`earliest`, `latest`):** Filtra por tiempo relativo a ahora.
    ```spl
    index="main" earliest=-24h@h latest=now
    ```
* **Selección Absoluta de Tiempo (`earliest`, `latest`):** Filtra por un rango de fechas/horas específico.
    ```spl
    index="main" earliest="2024-12-01T00:00:00" latest="2024-12-05T23:59:59"
    ```
* **Ordenar Resultados (`sort`):** Ordena los eventos. `-` para descendente, `+` (u omitido) para ascendente.
    ```spl
    index="main" | sort - _time  # Más reciente primero
    ```
    ```spl
    index="main" | sort +bytes, -count # Ordena por bytes (asc), luego por count (desc)
    ```
* **Limitar Resultados (`head`, `tail`):** Muestra solo los primeros o últimos N resultados.
    ```spl
    index="main" | head 10 # Primeros 10
    ```
    ```spl
    index=main | sort _time | tail 5 # Últimos 5 (después de ordenar por tiempo asc)
    ```

### Comandos de Agregación y Análisis Adicionales

* **`stats` (Suma):** Suma valores numéricos.
    * Descripción: Suma los `bytes` para cada `host` en el índice "web".
    ```spl
    index="web" | stats sum(bytes) as total_bytes by host
    ```
* **`timechart` (Promedio):** Calcula promedios a lo largo del tiempo.
    * Descripción: Muestra el promedio de `bytes` por hora en el índice "web".
    ```spl
    index="web" | timechart span=1h avg(bytes)
    ```
* **`top` (Valores Frecuentes):** Variante simple de `stats count by field | sort - count | head N`.
    * Descripción: Muestra las 10 `src_ip` más frecuentes en el índice "security".
    ```spl
    index="security" | top limit=10 src_ip
    ```
* **`stats` (Agrupación Múltiple):** Agrupa por más de un campo.
    * Descripción: Cuenta eventos agrupando por `status` y `host`.
    ```spl
    index="main" | stats count by status, host
    ```
* **`chart` (Gráficos Categóricos):** Similar a `stats` pero optimizado para gráficos (ej. barras apiladas).
    * Descripción: Genera datos para un gráfico de eventos por `status` a lo largo del tiempo.
    ```spl
    index="main" | chart count over _time by status
    ```

---

## 📚 Recursos Adicionales

* **Documentación Oficial de Splunk:** [https://docs.splunk.com/Documentation](https://docs.splunk.com/Documentation) - Guías detalladas sobre el uso de Splunk.
* **Splunk Community:** [https://community.splunk.com/](https://community.splunk.com/) - Comunidad activa para compartir conocimientos y resolver dudas.
* **Splunk Education:** [https://www.splunk.com/en_us/training.html](https://www.splunk.com/en_us/training.html) - Cursos y certificaciones oficiales.
* **TryHackMe:** [https://tryhackme.com/](https://tryhackme.com/) - Laboratorios prácticos y desafíos de ciberseguridad (incluye salas de Splunk).
* **Hack The Box:** [https://www.hackthebox.com/](https://www.hackthebox.com/) - Desafíos interactivos y pruebas de penetración (algunos escenarios usan Splunk).

---

## ❓ Contribuciones y Feedback

¿Ideas, correcciones, mejoras? Abre un **Issue** o un **Pull Request**. ¡Este recurso es para la comunidad SOC!
