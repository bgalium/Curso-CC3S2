# Actividad 3 - Parte Teórica: Reflexión Conceptual

Alumno : Walter Poma Fechas 15-09-2025

---

## 1. Introducción a DevOps: ¿Qué es y qué no es?

DevOps es una metodología que integra los equipos de desarrollo (Dev) y operaciones (Ops) para automatizar y acelerar la entrega de software. Basicamente es una estrategia con un ciclo de vida iterativo y continuo: **Plan -> Code -> Build -> Test -> Release -> Deploy -> Operate**, que es muy diferente al modelo Waterfall, que viene a ser un proceso lineal donde cada fase debe terminar para que la siguiente comience.

El lema "You build it, you run it" es un cambio de cultura. Sginifica que el equipo de desarrollo no solo va a ser responsable de escribir el codigo sino tambien del despliegue, operación y mantenimiento.

### Mitos vs. Realidades

* **Mito**: DevOps es solo un conjunto de herramientas de CI/CD.
    * **Realidad**: Las herramientas son habilitadoras, pero la base es el marco **CALMS (Culture, Automation, Lean, Measurement, Sharing)**. Sin una cultura de colaboración, las herramientas solo automatizan procesos deficientes.
* **Mito**: DevOps es un cargo o un equipo aislado.
    * **Realidad**: Es una mentalidad que impregna toda la organización. Los "Ingenieros DevOps" actúan como facilitadores para que los equipos de desarrollo adopten estas prácticas.

### Gate de Calidad en Makefile

Un gate de calidad es un control automático que bloquea un despliegue si no se cumplen criterios objetivos. 

## 2. MARCO CALMS

C - Culture (Cultura): La colaboración se fomenta al tener un repositorio Git único donde conviven el código, la automatización y la configuración de infraestructura. Todos los equipos trabajan con la misma fuente de verdad.

A - Automation (Automatización): El Makefile es el corazón de la automatización. Orquesta tareas como make deps (instalar dependencias) o make run (ejecutar la app), eliminando la intervención manual y los errores asociados.

L-Lean (metodología Lean): Los principios Lean se centran en eliminar el desperdicio. Por jeemplo un Makefile debería ser idempotente porque ejecutar mkae deps varias veces no debería causar efectos secundarios que noi se desean. El objetivo de este principio es entregar valor al usuario de la forma mas eficiente posible.

M-Measurement (Medición): Tomar deceisiones en base a datos. En el laboratorio se podría implementar a través de endpoints de salud en app.py, como /health y /ready, con curl, podemos medir la latencia y la tasa de errores.

S- Sharing (compartir): Es el principio que indica que elñ comnocimiento compartido rompe silos. En este caso el reposito Git es la principal herramienta para compartir código y configuración. El archivo Instrucciones.md es la base del conocimiento compartido. Para extender esto, se podría crear:

    Runbooks: este archivo sería para documentar pasoa a paso como diagnosticar y resolver problemas comunes.

    Postmortens: Una carpeta de postmortens sería para analizar incidentes , centrandose en las fallas del sistema y no de las personas.

## 3. Visión Cultural de Devops y paso a DevSecOps

Por concepto, se sabe que DevOps rompe los silos organizacionales al mantener una comunicación constante entre las áreas de trabajo, al promover objetivos y herramientas compartidas, estos silos son la causa principal de la lentitud en la entrega de software.

DevSecOps es la evolución de DevOps, que integra la seguridad como una responsabilidad compartida a lo largo del ciclo de vida del software, y la seguridad ya no es un control al final del proceso, sino una parte intrínseca desde elk diseñlo hasta la operación.

**Escenario retador: fallo certificado y mitigación cultural**

Hay un incidente: El certificado TLS de miapp.local expira porque el script de renovación automática falló. Los usuarios ven alertas de seguridad y el servicio es inaccesible.

La mitigación cultural sería que en eugar de culpar al equipo de operaciones, se realiza un postmortem sin culpas.

    Acciones Preventivas:

    1. Automatización: Un pipelioe de CI/CD que falla si el certificado no tiene validez por al menos 30 días.

    2. Medición: se añade un endpoinmt en app.py quye expone la fecha de expiración del certificado

    3. Compartir: Se comparte el procedimiento de renovación manual en un archivo de runbook.

**3 Controles de Seguridad para CI/CD**

1. Pruebas SAST: El control sería analizar el app.py para encontrar código inseguro, y su lugar en el CI/CD sería en la etapa de TEst/Build.

2. Análisis de Composición de Software: El control sería escanear las dependencias del proyectyo para detectar si se estan usando versión con vulnerabilidades ya conocidas. Su lugar en el CI/CD sería en el Build.

3. Validación de Configuración Segura (Hardening): Es un script que verifica que los archivos de configuración cumplen con las mejores prácticas de seguridad, su lugar en el CI/CD es en la etapa de Deploy.

## 4. Metodología 12-Factor App

1. III. Config

El principio es de separar estrictamente el código de la configuración, La implementación sería en el app.py se debe leer variables como PORT o la cadena de conexión a la base de datos desde variables de entorno (os.environ.get('PORT')), y el laboratorio fallaría si el app.py tuviera variables ya hardcodeadas por ejemplo PORT = 8080.

2. V. Build, Release, Run 

El principio es mantener una estricta separación entre esas 3 etapas. En Build se convierte el código en ejecutabel, en Release se combina ese ejecutable con la configuración del entorno y Run ejecuta. La implementación en el laboratorio  se dará así: el flujo git clone -> make deps es la etapa de build. La creación de un artefacto con git archive junto con las variables de entorno define una release inmutable. El comando make run o systemctl start miapp es la etapa de run. 

3. VII Port Binding

El principio es que la apllicación es autocontenida y expone sus servicios a través de uin puerto. La implementación será la aplicación Flask en app.py se vinculará a un puerto especificado por el entorno.

4. XI. Logs

El principio es que una aplicación 12-Factor nunca se preocupa por el enrutamiento o almacenamiento de sus logs. En su lugar, escribe su flujo de eventos, a la salida estándar (stdout) y al error estándar (stderr). La implementación es que la aplicación app.py solo usará print(),  El entorno de ejcución capturará el flujo de eventos y lo redirigirá a journal. La mejora sería agregar un colector de logs, que lerría los logs de journal y los enviaría a una plataforma para el análisis y almacenamiento.

