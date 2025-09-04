# Actividad 1: Introducción a DevOps/DevSecOps

- **Nombre** Walter Bryan Poma Navarro
- **Fecha** 2025-09-03
- **Tiempo Invertido** 

---

##Contexto del Entorno

Esta primera actividad será desarrollada en un entorno de Windows 11. La edición de los archivos markdown se hará en Visual Studio Code. La gestion de versiones se realizará con Git Bash.

---
### 4.1 DevOps vs. Cascada Tradicional

*(Aquí insertas tu diagrama comparativo)*
![Comparación DevOps vs Cascada](imagenes/devops-vs-cascada.png)

**Análisis Comparativo:**
El modelo en cascada se caracteriza por ser un proceso lineal y secuencial donde cada fase (requerimientos, diseño, implementación, pruebas, despliegue, mantenimiento) debe completarse antes de iniciar la siguiente. Es un modelo simple e idealista, la desventaja que tiene es que genera ciclos de entrega muy largos y si no le gusta al customer, debe pasar por el ciclo completo otra vez.

DevOps, en cambio, centra su metodología en un ciclo iterativo y continuo que integra el desarrollo y las operaciones, para unir personas, procesos y tecnología . Sus principales ventajas en entornos de nube son:
* **Feedback Continuo:** La integración y el despliegue continuos (CI/CD) permiten validar cambios en pequeños lotes, obteniendo retroalimentación casi instantánea de pruebas automatizadas y monitorización.
* **Reducción de Riesgo:** Al desplegar cambios pequeños y frecuentes, el "radio de impacto" (blast radius) de un error es menor y es más fácil identificar su causa y revertirlo (bajo MTTR).
* **Automatización:** La automatización de la construcción, pruebas y despliegue elimina errores manuales, acelera la entrega y libera a los equipos para que se enfoquen en tareas de mayor valor.

**Pregunta Retadora: ¿Cuándo es razonable un enfoque cercano a Cascada?**
Un enfoque similar a la cascada sigue siendo razonable en sistemas donde la **predictibilidad y la conformidad regulatoria** son más críticas que la velocidad. Un ejemplo claro es el desarrollo del **firmware para un dispositivo médico** (como un marcapasos) o el software de control de una planta nuclear.

* **Criterio Verificable 1 (Conformidad):** Aprobación del 100% de las auditorías regulatorias (ej: FDA, ISO 13485) antes de la liberación de cualquier versión. El proceso debe generar documentación exhaustiva para cada fase.
* **Criterio Verificable 2 (Fiabilidad):** Tasa de fallos críticos en producción debe ser de 0%. Esto justifica un ciclo de pruebas manuales y validación extremadamente riguroso y prolongado.

* **Trade-offs:** Se sacrifica la **velocidad de entrega** (meses o años por versión) a cambio de una **máxima fiabilidad y seguridad** auditada. En estos contextos, un error no solo es un bug, sino que puede tener consecuencias fatales.

---

### 4.2 Ciclo Tradicional de Dos Pasos y Silos

![Silos Organizacionales](imagenes/silos-equipos.png)

El ciclo tradicional "construcción -> operación" sin integración continua crea silos donde los equipos de desarrollo y operaciones trabajan de forma aislada, con objetivos y métricas diferentes.

**Dos Limitaciones Principales:**
1.  **Integración Tardía y en Grandes Lotes:** El código de múltiples desarrolladores se integra al final del ciclo, lo que provoca conflictos complejos y un alto "costo de integración tardía". Los defectos se acumulan y se descubren justo antes del despliegue, creando cuellos de botella.
2.  **Asimetría de Información y Handoffs:** El equipo de desarrollo "entrega" el software al equipo de operaciones (un *handoff*). Operaciones no siempre tiene el contexto completo del código, y Desarrollo no tiene visibilidad de los problemas en producción. Esto genera un juego de culpas y lentitud en la resolución de incidentes.

**Pregunta Retadora: Dos Anti-patrones y su Impacto**
* **Anti-patrón 1: "Throw over the wall" (Lanzar por encima del muro):** Es la práctica de que Desarrollo finaliza el código y lo pasa a Operaciones sin mayor responsabilidad sobre su despliegue o funcionamiento. Esto agrava los incidentes porque Operaciones carece del conocimiento para depurar fallos complejos, incrementando el **MTTR (Tiempo Medio de Recuperación)**, ya que deben escalar el problema de vuelta a un desarrollador que ya cambió de contexto.
* **Anti-patrón 2: Seguridad como Auditoría Tardía:** La seguridad se involucra solo al final del ciclo, justo antes de pasar a producción. Esto convierte a seguridad en un "portero" que bloquea lanzamientos. Cuando se encuentran fallos críticos, se generan **retrabajos costosos** y urgentes, o peor, se aceptan riesgos por la presión de cumplir fechas, acumulando deuda técnica.

---

### 4.3 Principios y Beneficios de DevOps

**CI/CD, Automatización y Colaboración:**
* **Integración Continua (CI):** Es la práctica de integrar cambios de código de forma frecuente (varias veces al día) en un repositorio central. Cada integración dispara una **construcción y pruebas automatizadas** (unitarias, de integración) cercanas al código. Su objetivo es detectar errores de integración de forma temprana.
* **Despliegue Continuo (CD):** Es la extensión de CI. Una vez que los cambios pasan todas las pruebas automatizadas, se despliegan automáticamente en un entorno (pruebas, producción). Esto requiere un alto grado de confianza en la automatización y permite que **cambios pequeños** lleguen a los usuarios rápidamente.

**Agile como Precursor:**
Una práctica Agile como la **retrospectiva** alimenta directamente el pipeline de CI/CD. Si en una retrospectiva el equipo identifica que muchos bugs se deben a una configuración incorrecta en el entorno de pruebas, pueden decidir **agregar una nueva etapa de pruebas automatizadas al pipeline** que verifique la configuración antes del despliegue. Así, una decisión de colaboración y mejora continua se traduce en una mejora tangible y automatizada.

**Indicador Observable para Medir Colaboración:**
* **Indicador:** Reducción del **"Cycle Time"** desde que una Pull Request (PR) es aprobada hasta que el cambio está desplegado y validado en un entorno de pre-producción.
* **Cómo recolectarlo (sin herramientas pagas):** Se puede medir utilizando los metadatos de Git y los registros de un servidor de CI/CD simple (como Jenkins o GitLab CI).
    1.  **Timestamp de Aprobación de PR:** Extraer de la API de GitHub/GitLab.
    2.  **Timestamp de Despliegue Exitoso:** Obtener del log del script de despliegue que se ejecuta en el pipeline.
    3.  **Cálculo:** Restar ambos timestamps. Una disminución sostenida de este tiempo indica que los *handoffs* entre Dev y Ops se están reduciendo y el proceso es más fluido.

---

### 4.4 Evolución a DevSecOps

**SAST vs. DAST:**
* **SAST (Static Application Security Testing):** Analiza el **código fuente en reposo**, sin ejecutarlo. Es como un corrector de gramática para el código que busca patrones de vulnerabilidades conocidas (ej: inyección SQL). Se ubica **muy temprano en el pipeline**, idealmente en el entorno del desarrollador o al hacer un commit.
* **DAST (Dynamic Application Security Testing):** Analiza la **aplicación en ejecución**, simulando ataques externos. No ve el código, sino que interactúa con la aplicación como lo haría un atacante (ej: probando formularios). Se ubica **más tarde en el pipeline**, cuando la aplicación ya está desplegada en un entorno de pruebas o staging.

**Gate Mínimo de Seguridad:**
Un gate de seguridad es un punto de control automatizado en el pipeline.
* **Umbral 1 (SAST):** El pipeline se **bloquea** si el análisis SAST detecta **CUALQUIER vulnerabilidad de severidad "Crítica" (CVSS ≥ 9.0)** en el código que será expuesto a internet.
* **Umbral 2 (DAST):** Se requiere una **cobertura mínima del 85%** de los endpoints críticos (autenticación, transacciones) en las pruebas DAST. Si la cobertura es menor, el pipeline falla.

**Política de Excepción:**
* **Condición:** Una vulnerabilidad crítica detectada por SAST no puede ser corregida inmediatamente por una dependencia externa.
* **Política:** Se puede otorgar una excepción **por un máximo de 14 días**.
* **Responsable:** Debe ser aprobada por el Líder Técnico y el Oficial de Seguridad.
* **Plan de Corrección:** Se debe documentar un plan de mitigación temporal (ej: una regla en el WAF) y asignar un ticket para la corrección definitiva antes de la fecha de caducidad.

**Pregunta Retadora: ¿Cómo evitar el "Teatro de Seguridad"?**
El "teatro de seguridad" es cumplir con el checklist sin reducir el riesgo real. Para evitarlo, nos enfocamos en resultados:
* **Señal de Eficacia 1 (Reducción de Recurrencia):** Medir la **tasa de reaparición de vulnerabilidades** de la misma clase. Si el equipo corrige una inyección SQL pero introduce otra similar en el siguiente sprint, las prácticas no han mejorado. El objetivo es una tasa de recurrencia < 5% por trimestre. Se mide comparando informes de SAST a lo largo del tiempo.
* **Señal de Eficacia 2 (Reducción del Tiempo de Remediación):** Medir el tiempo desde que una vulnerabilidad es detectada por el pipeline hasta que el fix es mergeado. Un tiempo de remediación para vulnerabilidades críticas que consistentemente sea **menor a 48 horas** indica que la seguridad es una prioridad integrada y no una tarea de baja prioridad.

---

